# Testing using RSpec file

RSpec is a tool that is used for testing. Here tests are called as "specs". It often read almost like a plain english, making it easier to userstand what's being tested and why.

To setup the RSpec first we need to add gemfiles.

```ruby
group :development, :test do
  gem 'rspec-rails', '~> 6.0'  # or the latest version
  gem 'factory_bot_rails'      # optional, but very handy for creating test data
end
```

Once after adding these in gemfile we need to bundle install them and after that run the following command

```bash
rails generate rspec:install
```
Once we do that we will be getting a folder called "spec". Inside that spec we have folders named models, controllers, views, etc. If we don't find we can create those folders in the spec folder and we can create related files inside the specific folders.

For example you have a model named "User" and you want to create a spec dor the user you can create `user_spec.rb` file inside `spec/models` folder.

### Model Spec
Model specs typically test validations, associations, and instance/class methods in your model.

Example:
```ruby
# spec/models/post_spec.rb
RSpec.describe Post, type: :model do
  describe "validations" do
    it "requires a title" do
      post = Post.new(title: nil)
      expect(post.valid?).to be_falsey
      expect(post.errors[:title]).to include("can't be blank")
    end
  end

  describe "associations" do
    it { should belong_to(:user) }
    it { should have_many(:comments) }
  end
end
```
**Note**: The one-liner syntax with it { should ... } comes from the shoulda-matchers gem (often used in combination with RSpec).

### Request Spec
Request specs let you test the routing, controller actions, and responses for your endpoints


Example:
```ruby
# spec/requests/posts_spec.rb
RSpec.describe "Posts API", type: :request do
  let(:user) { User.create!(name: "Test User") }
  let!(:post1) { Post.create!(title: "First Post", user: user) }

  describe "GET /posts" do
    it "returns a list of posts" do
      get "/posts"  # or rails route helper if you prefer: get posts_path
      expect(response).to have_http_status(:ok)
      expect(JSON.parse(response.body).size).to eq(1)
    end
  end
end
```

### Feature/System Spec

If you need to test the entire UI flow (e.g., filling forms, clicking buttons), you can use system specs (or older style feature specs) with [Capybara](https://github.com/teamcapybara/capybara).

Example:

```ruby
# spec/system/user_login_spec.rb

require 'rails_helper'

RSpec.describe "User Login Flow", type: :system do
  before do
    driven_by(:rack_test)  # or :selenium_headless if you want to run in a browser
  end

  it "logs the user in and redirects to the dashboard" do
    user = create(:user, email: "john@example.com", password: "password123")

    visit login_path
    fill_in "Email", with: user.email
    fill_in "Password", with: "password123"
    click_button "Log in"

    expect(page).to have_content("Welcome, #{user.name}")
  end
end
```

### Factories with FactoryBot
Hard-coding data in your tests can get repetitive. FactoryBot helps you create records with default data.

Example:

```ruby
# spec/factories/users.rb
FactoryBot.define do
  factory :user do
    name { "John Doe" }
    email { "john@example.com" }
    password { "secretpass" }
  end
end
```

Then in my spec I use
```ruby
let(:user) { create(:user) }
```

To run all the specs we use

```bash
bundle exec rspec
```

To run a specific spec we do the following

```bash
bundle exec rspec spec/models/post_spec.rb
```

You can also run a single line by appending :LINE_NUMBER, like

```bash
bundle exec rspec spec/models/post_spec.rb:12
```


### A Few Best Practices

1. **Keep Tests Fast**
Try not to rely on too many external services or complex data setups. Use mocks/stubs where it makes sense.
2. **Test All the Important Things**
Especially important business logic. But be careful not to over-test trivial code or rails internals.
3. **Use `before` Hooks and Shared Contexts**
It reduces duplication in your tests.
```ruby
before(:each) do
  @user = create(:user)
end
```
4. **Keep Tests Independent**
Each spec should be isolated. One test’s result shouldn’t rely on a previous test.
5. **Be Clear and Descriptive**
Spec names should read naturally (ex: `it "returns a 404 for unknown records"`) and describe exactly what’s happening.