# Setting Postgres as Database.

Here, we will look into how to set postgres as a database. By default when you set up rails, you will have sqllite as the database. To switch that we need to do following steps.

First, we need to replace 'sqlite' to 'pg' in Gemfile.

```ruby
gem "sqlite3", "~> 1.4" # Comment this line of code.
```
You should coment or remove the line of code that should look something like this.

```ruby
gem "pg", "~> 1.2", ">= 1.2.3"
```
Now add this line in the Gemfile and run the below command in your terminal.

```bash
~/learning main* ❯ bundle install
```
Now you need to change the dabase configuration which is present in `config/database.yml`.  

In this file you configure the database for all your environments like Development, Testing and Production.

The file should look something like this after your changes.

```yml
default: &default
  adapter: postgresql
  encoding: unicode
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: chetan
  password: 123456
  host: localhost

development:
  <<: *default
  database: test_db_development


test:
  <<: *default
  database: test_db_test

production:
  <<: *default
  database: test_db_production

```

That's it. Make sure you have those DBs are created and you are good to go.
