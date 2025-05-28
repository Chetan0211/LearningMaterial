# How to stream video in rails

In here we are going to learn how to stream a video.
To do that we need to follow some steps:
1. Upload the video source from user directly to the remote storage.
2. Transcode the video from the original file for different bitrate and save it.
3. Get the transcode video to the frontend to stream the video.

Before going to the step let's setup the rails project.

For this we will be needing ActiveStorage to store metadata of a video.

```bash
> rails active_storage:install
> rails db:migrate
```

Now, inside the model add the attachment like
```ruby
has_one_attached :video
```

Next step you need a cloud storage to store the video files. You can also use local storage to store the video files. But in productions it's best to use cloud storage.

Let's use Cloudflare R2 to store the video. R2 uses same S3 API protocals store or read from the bucket.

We need to add `aws-sdk-s3` gem file and install the bundle.

```gemfile
gem 'aws-sdk-s3', '~> 1'
```

In Cloudflare website we need to create a bucket and get an public URL with access key and secret access key. Once you got all those you need to save those in `storage.yml` file.

```yaml
cloudflare_r2:
  service: S3
  endpoint: <%= ENV["CLOUDFLARE_R2_ENDPOINT"] %>
  access_key_id: <%= ENV["CLOUDFLARE_R2_ACCESS_KEY_ID"] %>
  secret_access_key: <%= ENV["CLOUDFLARE_R2_SECRET_ACCESS_KEY"] %>
  region: auto
  bucket: <%= ENV["CLOUDFLARE_R2_BUCKET_NAME"] %>
  request_checksum_calculation: "when_required"
  response_checksum_validation: "when_required"
```

> **NOTE**: We have added request_checksum_calculation: "when_required" and response_checksum_validation: "when_required" because without that we will get the multiple checksum error.

Now in `config/development.rb` we need to set `config.active_storage.service = :cloudflare_r2`. Now ActiveStorage 

### Upload the video source from user directly to the remote storage.
Generally when ever we use ActiveStorage to attach a file, it will go to the backend and from there the data get's stored.
But for a video to save going from a backend is not ideal because it interupts the process as it takes time. So we directly save the video user frontend to the buckets like S3.

For uploading directly from front end to bucket we will be using `activestorage.js`.

We need to pin `@rails/activestorage` in importmaps.

```bash
> bin/importmap pin @rails/activestorage
```

This will add the file in `vendor/javascript/@rails--activestorage.js` and that file is pinned in `importmap.rb`

```ruby
pin "@rails/activestorage", to: "@rails--activestorage.js" # @8.0.200
```

Now in `application.js` file we need to add the following lines.
```javascript
import * as ActiveStorage from "@rails/activestorage"
ActiveStorage.start()
```

Now in your views call the file field with `direct_upload: true`

```erb
<%= form.file_field :attachments, multiple: true, direct_upload: true %>
```

If you are not using form builder then we can do this by simple html too.
```html
<input type="file" data-direct-upload-url="<%= rails_direct_uploads_url %>" />
```

Thats it, now when you sumbit the file it will directly store in your storage without going through rails backend.

Direct upload provides event listeners to track the direct upload of a file.

```javascript
// here video_input is the input field tag
video_input.addEventListener('direct-upload:initialize', () => {
  // this triggers after initialization of the upload
});

video_input.addEventListener('direct-upload:start', () => {
  // this triggers once upload gets started
});

video_input.addEventListener('direct-upload:finish', () => {
  // this triggers once the upload in finished
});

video_input.addEventListener('direct-upload:error', () => {
  // this triggers when upload is an error
});

video_input.addEventListener('direct-upload:progress', (e) => {
  // this is used to cehck the progress. Yu can use e.detail.progress to track from 0 to 100.
});
```

So now once you submit the form, then direct-upload kicks up and helps you store the file directly to your bucket.

### Transcode the video from the original file for different bitrate and save it.

Now, after we save the video in a storage we need to transcode to different bitrate and format so that when streaming we can set the bitrate according to the internet speed.

To do that we need to use `FFmpeg` which is a free and open-source command-line tool used for manipulating video, audio, and other multimedia files and streams.

This package needs to be installed where ever we will be runnning the worker node that is used to transcode.

you can use `apt` to install  `ffmpeg` in derbian/linux distributions. If you are using mac you can do it using `brew install ffmpeg`.

Now to transcode we will use a background job, because it takes time and we won't want to wait server to complete it.

So we need to create a job. Let's create transcoding job `bin/rails g job transcoding` will create `transcoding_job.rb` file.

In this file you need to require 'fileutils' and 'open3'.

> **NOTE**: fileutils is mainly used when you need to manipulate the files and folders locally like to create and update with folders and files. Mainly used if we are using local storage.

We need a target resolution 
```ruby
TARGET_RESOLUTIONS = [
    [360, "800k", "96k", 640],
    [480, "1400k", "128k", 854],
    [720, "2800k", "128k", 1280],
    [1080, "5000k", "192k", 1920]
  ].freeze
```
Inside the perform method we need to create a temporary directory to store all the files that are created according to resolution temporarily before saving into a bucket.

We need to download original file from the bucket and we need to loop through the target resolutions and we will be using `open3` to run ffmpeg command in commanline.
`ffmpeg` is used to convert the original video format to desired video format and bitrate.

Once those are done we need to save all the file in the bucket in the desired folder structure.

Finally we will be having a master file that has information about all the resolutions data and save that file too as a root file and save the URL for that file in your databse.

That's it. Now you can save the video files and transcibe it.

```ruby
require 'fileutils'
require 'open3'
class TranscodeVideoJob < ApplicationJob
  queue_as :video

  TARGET_RESOLUTIONS = [
    [360, "800k", "96k", 640],
    [480, "1400k", "128k", 854],
    [720, "2800k", "128k", 1280],
    [1080, "5000k", "192k", 1920]
  ].freeze

  R2_HLS_OUTPUT_BASE_KEY_PREFIX = "processed_hls".freeze

  def perform(**args)
    post = Post.find(args[:post_id])
    return unless post.video.attached?

    unless defined?(S3_CLIENT_R2)
      Rails.logger.error "Cloudflare R2 not configured. Aborting transcoding for Post ID #{post.id}."
      post.update!(status: :failed)
      return
    end

    Dir.mktmpdir("local-transcoding-#{post.id}-") do |ffmpeg_temp_processing_dir|
      original_file_path = download_original_file_locally(post, ffmpeg_temp_processing_dir)
      return unless original_file_path
      r2_hls_post_key_prefix = File.join(R2_HLS_OUTPUT_BASE_KEY_PREFIX, "videos", post.id.to_s)
      stream_playlist_details_for_master = []
      TARGET_RESOLUTIONS.each do |height, v_bitrate, a_bitrate, width_16_9|
        Rails.logger.info "Transcoding Post ID #{post.id} to resolution #{height}p."
        resolution_output_dirname = "#{height}p"
        resolution_temp_output_dir = File.join(ffmpeg_temp_processing_dir, resolution_output_dirname)
        FileUtils.mkdir_p(resolution_temp_output_dir)
        level_for_resolution = case height
        when 360 then "3.0"
        when 480 then "3.1"
        when 720 then "3.2" # Or 4.0 if aiming for higher FPS support
        when 1080 then "4.1" # Or 4.2 for 60fps
        else "4.0" # A sensible default
        end

        stream_playlist_filename = "stream.m3u8"
        segment_filename_pattern = "segment_%05d.ts"
        cmd_array = [
          "ffmpeg",
          "-i", original_file_path,
          "-vf", "scale=w=#{width_16_9}:h=#{height}:force_original_aspect_ratio=decrease,pad=#{width_16_9}:#{height}:(ow-iw)/2:(oh-ih)/2:color=black,format=yuv420p",
          "-c:v", "libx264", 
          "-profile:v", "high", 
          "-level", level_for_resolution, 
          "-preset", "medium",
          "-b:v", v_bitrate, 
          "-maxrate", v_bitrate, 
          "-bufsize", (bitrate_to_int(v_bitrate) * 2 / 1000).to_s + "k", # Ensure bufsize is appropriate, e.g., 2 * v_bitrate
          "-c:a", "aac", 
          "-b:a", a_bitrate,
          "-g", "48", 
          "-keyint_min", "48", 
          "-sc_threshold", "0",
          "-hls_time", "4", 
          "-hls_playlist_type", "vod",
          "-hls_segment_filename", File.join(resolution_temp_output_dir, segment_filename_pattern),
          File.join(resolution_temp_output_dir, stream_playlist_filename)
        ]

        _stdout, stderr, status = Open3.capture3(*cmd_array)

        unless status.success?
          Rails.logger.error "FFmpeg failed for #{height}p (Video ID #{post.id}): #{stderr}"
          next
        end

        resolution_r2_key_prefix = File.join(r2_hls_post_key_prefix, resolution_output_dirname)

        Dir.glob(File.join(resolution_temp_output_dir, "*")).each do |file_path|
          file_name = File.basename(file_path)
          r2_object_key = File.join(resolution_r2_key_prefix, file_name)
          content_type = case File.extname(file_name)
                        when ".m3u8" then "application/vnd.apple.mpegurl"
                        when ".ts"   then "video/MP2T"
                        else "application/octet-stream"
                        end
          upload_to_r2(file_path, r2_object_key, content_type)
        end
        Rails.logger.info "Finished transcoding Post ID #{post.id} to resolution #{height}p."
        stream_playlist_details_for_master << {
          # Change: Path is now relative to the master playlist's location in R2
          path: "#{resolution_output_dirname}/#{stream_playlist_filename}",
          bandwidth: calculate_bandwidth(v_bitrate, a_bitrate),
          resolution: "#{width_16_9}x#{height}"
        }
      end

      Rails.logger.info "Generating master playlist for Post ID #{post.id}."

      if stream_playlist_details_for_master.empty?
        post.update!(status: :failed, processed_video_url: nil)
        Rails.logger.error "No HLS streams were successfully transcoded for Video ID #{post.id}."
        return
      end
      master_playlist_filename = "master.m3u8"
      master_playlist_content = generate_master_playlist_content(stream_playlist_details_for_master)
      master_playlist_r2_key = File.join(r2_hls_post_key_prefix, master_playlist_filename)
      upload_to_r2(nil, master_playlist_r2_key, "application/vnd.apple.mpegurl", master_playlist_content)
      processed_video_public_url = File.join(CLOUDFLARE_R2_CONFIG[:pub_endpoint], master_playlist_r2_key)
      post.update!(
        status: :published,
        processed_video_url: processed_video_public_url # Change: URL points to R2
      )
      Rails.logger.info "HLS Transcoding completed for Video ID: #{post.id}. Master Playlist R2 URL: #{processed_video_public_url}"
    end
  rescue StandardError => e
    Rails.logger.error "Unhandled error in TranscodingJob for Post ID #{args[:post_id]}: #{e.message}\n#{e.backtrace.join("\n")}"
    post&.update!(status: :failed)
  end

  private

  def download_original_file_locally(post, temp_dir)
    original_r2_key = post.video.key

    unless original_r2_key.present?
      Rails.logger.error "Original video R2 key is missing for Post ID #{post.id}."
      post.update!(status: :failed)
      return nil
    end

    original_filename = post.video.filename.to_s
    download_path = File.join(temp_dir, original_filename)
    begin
      S3_CLIENT_R2.get_object(
        bucket: CLOUDFLARE_R2_CONFIG[:bucket_name],
        key: original_r2_key,
        response_target: download_path
      )
      return download_path
    rescue StandardError => e
      post.update!(status: :failed)
      return nil
    end
  end

  def upload_to_r2(file_path, key, content_type, body_content = nil)
    begin
      options={
        bucket: CLOUDFLARE_R2_CONFIG[:bucket_name],
        key: key,
        content_type: content_type,
        acl: "public-read"
      }
      if body_content
        options[:body] = body_content
      elsif file_path
        options[:body] = File.open(file_path, 'rb')
      else
        raise ArgumentError, "Either file_path or body_content must be provided."
      end
      S3_CLIENT_R2.put_object(options)
    rescue Aws::S3::Errors::ServiceError => e
      Rails.logger.error "R2 Upload Error for key #{key}: #{e.message}"
      raise
    ensure
      options[:body].close if options[:body].is_a?(File) && !options[:body].closed?
    end
  end

  def generate_master_playlist_content(stream_details)
    content = ["#EXTM3U", "#EXT-X-VERSION:3"]
    stream_details.sort_by! { |s| s[:bandwidth] }
    stream_details.each do |stream|
      content << "#EXT-X-STREAM-INF:BANDWIDTH=#{stream[:bandwidth].to_i},RESOLUTION=#{stream[:resolution]}"
      content << stream[:path]
    end
    content.join("\n")
  end

  def bitrate_to_int(bitrate_str)
    val = bitrate_str.to_i
    val *= 1000 if bitrate_str.downcase.include?('k')
    val *= 1000000 if bitrate_str.downcase.include?('m')
    val
  end

  def calculate_bandwidth(video_bitrate_str, audio_bitrate_str)
    # Per HLS authoring spec, BANDWIDTH should be the peak, not average.
    # A common heuristic is 1.1x to 1.5x of the average combined bitrate.
    # Let's use 1.25x as a reasonable factor.
    # FFmpeg's -maxrate and -bufsize help control peaks, but this is still an estimation.
    (bitrate_to_int(video_bitrate_str) + bitrate_to_int(audio_bitrate_str)) * 1.25
  end
end

```

> **NOTE**: In ffmpeg under -vf i have added format=yuv420p because if the video is 10 bit it will convert to 8 bit for transcode, because 10 bit is more complex so it fails with curent resource. But if you make more resources(means making profile-v as high10 and all) it is not necessary for a normal 8 bit video. So, for now it is always a good trade. If future if we get more exposure on video we can make more make more complex calls.

