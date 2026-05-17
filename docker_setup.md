# Setup Docker

Before settigng up docker we need to know about what is docker and some naming convensions used inside docker and why it is such a handy tool.

[Click Here](https://docs.docker.com/get-started/docker-overview/) to know about docker.

After reading from the docker website now you will be having a good knowledge on hwat is docker and how it works. Now we learn how to use docker in our projects so that we can run our application in production or any other machine easily without much effort.

We need to have two files in our project to make the docker work.

1. **Dockerfile**: This file is like a blueprint for a single container. Here we outline the OS, the dependencies, and the exact steps needed to build your appliction image.

2. **docker-compose.yml**: THis file is used to link the seperate runnables for example if we need to run rails, postgresql, redis and sidekiq we don't need to run in 4 seperate terminals, instead we will specify everything in this file and run so that it will run and link everything using one file.



Let's discuss about Dockerfile with an example. 

Consider we need to run a rails application with postgresql, redis and sidekiq.

Let's look into the Dockerfile line by line to understand better.

If you don't find Dockerfile in your rails project you can run Docker init to create Dockerfile with build-in content.


```Dockerfile
FROM ruby:3.3.0-slim AS base
```

Here we are setting up the base image for the container.The -slim tag means it uses a stripped-down version of Debian Linux to keep the image size small and efficient. We are labeling this stage as `base`.

```Dockerfile
WORKDIR /rails
```
Used to create and sets the active directory inside the container filesystem to `/rails`.

```Dockerfile
ENV RAILS_ENV=production \
    BUNDLE_DEPLOYMENT=1 \
    BUNDLE_PATH=/usr/local/bundle \
    BUNDLE_WITHOUT=development
```
It sets global production environment variables.

* `RAILS_ENV=production` Makes rails run on production environment and optimize rails for maximum caching and speed.
* `BUNDLE_DEPLOYMENT=1` locks down dependencies, forcing Bundler to strictly match your `Femfile.lock`.
* `BUNDLE_WITHOUT=development:test`: Tells Bundler not to install tools you only use locally (like debuggers or test suites), keeping the footprint tiny.

```Dockerfile
FROM base AS build
```
Starts a new, seperate stage called build, inheriting everything from our clean `base` configuration.

```Dockerfile
RUN apt-get update -qq &&  apt-get install --no-install-recommends -y build-essential \
git \
libpq-dev \
libvips \
pkg-config

```

Here we install all the linux system dependencies the app needs to compile gems and run

* `apt-get update -qq`: Refreshes the packages list silently.
* `build-essential`: provides compilers(like `gcc` and `name`) needed to compile native C extensions found in gems like `nokogiri` or `bcrypt`.
* `libpq-dev`: It provides the PostgreSQL client library headers so the pg gem can talk to your database.
* `libvips`: A fast image processing library that Rails Active Storage uses for resizing and processing image uploads.
* `git`: To perform git actions.
* `pkg-config`: Helps compilers locate the installed libraries.
* `rm -rf /var/lib/apt/lists/*`: Deletes the downloaded packages lists after installation to keep the final image size lean.

```Dockerfile
COPY Gemfile Gemfile.lock ./
```

Copies just the dependency files into the container workspace.

```Dockerfile
RUN bundle install && \
    rm -rf  ~/.bundle/ "${BUNDLE_PATH}"/ruby/*/cache "${BUNDLE_PATH}"/ruby/*/bundler/gems/*/.git && \
    bundle exec bootsnap precompile --gemfile
```

* `bundle install`: Used to download and install all production gems.

* `rm -rf`: Cleans up and deletes downloaded `.gem` cache files and hidden git repository folders to save space.

* `bootsnap precompile --gemfile`: Optimizes gem loading sequences so your application boots significantly faster.

```Dockerfile
COPY . .
```
Copies the rest of the rails source code from your local computer(or repo) into the container.

```Dockerfile
RUN bundle exec bootsnap precompile app/ lib/
```
Pre-compiles your core application logic (app/ and lib/) into rapid-loading cache artifacts. When your application reboots or scales up in production, it will load almost instantly.

```Dockerfile
RUN SECRET_KEY_BASE_DUMMY=1 ./bin/rails assets:precompile
```
Minifies and compiles your assets (CSS, JS, images) into public production files. Rails normally crashes during this step if a secure master key isn't present; passing `SECRET_KEY_BASE_DUMMY=1` allows the assets to build safely without exposing your real secret keys during the build process.

```Dockerfile
FROM base
```
Starts a fresh stage, inheriting directly from our ultra-clean base configuration. Notice it does not inherit from the heavy build stage.

```Dockerfile
RUN apt-get update -qq && \
    apt-get install --no-install-recommends -y curl libpq5 libvips && \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/archives/*
```
Install only runtime dependencies.

* `curl`: Helpful for internal health checks.
* `libpq5`: Installs postgresql runtime.
* `libvips`: THe core engine needed to process user-uploading images to runtime.
* `rm -rf ...`: Deletes downloading installer caches completely so the final image stays increadibly compact.

```Dockerfile
COPY --from=build /usr/local/bundle /usr/local/bundle
COPY --from=build /rails /rails
```
This is where the multi-stage magic happens. This reaches back into the temporary `build` workshop container and copies over only the fully compiled production gems and the clean application source code(including your precompiled assets). It leaves the heavy compilers(`build-essential`, `git`) behind.

```Dockerfile
RUN useradd rails --create-home --shell /bin/bash && \
    chown -R rails:rails db log storage tmp
USER rails:rails
```
This is used for security hardening. By default, docker runs everything as a powerful `root` superuser. This creates a restricted system user named `raila`, gives them ownership of the only folders rails needs to write to, and switched the active user context to `rails`. Is someone hack the app, theya re trapped in the restricted user account.

```Dockerfile
COPY bin/docker-entrypoint /usr/bin/
ENTRYPOINT ["docker-entrypoint"]
```
Sets up the startup script wrapper to wipe out lingering `server.pid` files.

```Dockerfile
EXPOSE 3000
CMD ["rails", "server", "-b", "0.0.0.0"]
```