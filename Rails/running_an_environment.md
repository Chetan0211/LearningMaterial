# Run an Environment

We know when we want to run the application we just use `bin/rails s`. But consider there is a point where you need to be running different things before running the application and you need to do all those things separately in a terminal like running tailwind watch and running sidekiq etc. To make this easiler we will be using something called `Prockfile.dev`(You have to creating the Prockfile.dev if not present inside the application root folder). In this file we will be mentioning all the runnables. It should be looking like this:

```procfile
web: bin/rails server
css: bin/rails tailwindcss:watch
```

Here you see something like `web:` and `css:`. These are identifiers and others things are commands. This file helps which commands has to be run for starting an application.

Now after creating the Procfile we need to create the dev file inside `/bin` folder. Inside the dev copy the following commands.

```shell
#!/usr/bin/env sh
if ! gem list foreman -i --silent; then
  echo "Installing foreman..."
  gem install foreman
fi
# Default to port 3000 if not specified
export PORT="${PORT:-3000}"
# Let the debug gem allow remote connections,
# but avoid loading until `debugger` is called
export RUBY_DEBUG_OPEN="true"
export RUBY_DEBUG_LAZY="true"
exec foreman start -f Procfile.dev "$@"
```

After this to run the application in terminal just run the command
```bash
./bin/dev
```
That's it, it will run all the commands you mentioned in Procfile.dev.

Note: When you run the `./bin/dev` if you get something like permission denied then you just run `chmod +x ./bin/dev` so that you will be giving permissions. Then you can run the command again. This time it will run smoothly.

Note: When you run using `./bin/dev` you cannot debug as this line will run multiple services. When you add debugger line in code you can interact in terminal but you need to use new terminal. And inside that type "rdbg -A"(This will work when you are using debug gem as a debugger.)