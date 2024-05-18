# Using of devise gem

In rails devise is used to authenticate the user. </br>
First we need to add the gem file in `gemfile`.
```gemfile
gem 'devise'
```
Once we add it we need to do generate the bundle files.
```shell
bundle install
```
Then, we need to install devise.
```shell
rails g devise:install
```
Once we install we can able to create the table that used by devise. In this example we need to add users as devise.
``` shell
bin/rails g devise user
```