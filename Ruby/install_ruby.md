In this installation guide you will see installation process of ruby.

# Installing Ruby in Linux Environment
first open your terminal and run the update command.
```bash
sudo apt-get update
```
Once it's done you need to install dependencies to for compelling ruby.
```bash
sudo apt-get install git-core zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev
```

Now for version manager we are using ASDF. So we need to install it and add that to the shell.

To install ASDF use below commands

```bash
cd
git clone https://github.com/excid3/asdf.git ~/.asdf
echo '. "$HOME/.asdf/asdf.sh"' >> ~/.bashrc
echo '. "$HOME/.asdf/completions/asdf.bash"' >> ~/.bashrc
echo 'legacy_version_file = yes' >> ~/.asdfrc
echo 'export EDITOR="code --wait"' >> ~/.bashrc
exec $SHELL
```

Once ASDF is installed you first need to add ruby and node.

```bash
asdf plugin add ruby
asdf plugin add nodejs
```

Once you added those two, you need to then install ruby with latest version. At the time of preparing this version is 3.3.4

```bash
asdf install ruby 3.3.4
asdf global ruby 3.3.4
# Update to the latest Rubygems version
gem update --system
```

Then install node
```bash
asdf install nodejs 20.17.0
asdf global nodejs 20.17.0
# Install yarn for Rails jsbundling/cssbundling or webpacker
npm install -g yarn
```

Then you can check if these two are installed by following command

```bash
ruby -v # 3.3.4
node -v # 20.17.0
```

For more info and latest installation visit: [GoRails](https://gorails.com/setup/ubuntu/24.04)