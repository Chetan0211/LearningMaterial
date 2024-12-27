# Change to UUID from ID

In here we will be seeing how to use uuid as your id if you are using postgres. 

If you just created the application and you need to change to UUID then you need to do these simple steps.

Create a migration file named EnablePgCryptoExtension by

```bash
rails generate migration EnablePgCryptoExtension
```

Then you will see new file in migrations, change the following code.

```ruby
class EnablePgCryptoExtension < ActiveRecord::Migration[7.1]
  def change
    enable_extension 'pgcrypto'
  end
end

```

In your `config/applications.rb` file you need to add

```ruby
config.active_record.primary_key = :uuid
```

After that do the migration. Once that is done you can able to specify the model with id to use uuid.

example:

```ruby
class AddDeviseToUsers < ActiveRecord::Migration[7.1]
  def self.up
    change_table :users, id: :uuid do |t|
      # table columns goes  here
    end
  end
end
```

If you already have tables and you need to change the id to uuid you need to do the following steps:

1. Need to add the UUID to the table
```ruby
class AddUuidToUsers < ActiveRecord::Migration[6.1]
  def change
    add_column :users, :uuid, :uuid, default: 'gen_random_uuid()'
  end
end
```

2. You need to remove the id and need to change uuid to id

```ruby
class SwitchUsersToUuid < ActiveRecord::Migration[6.1]
  def up
    # 1. Add the UUID column (if you haven't done so yet)
    add_column :users, :uuid, :uuid, default: 'gen_random_uuid()', null: false

    # 2. Switch out references (this can involve multiple steps if you have foreign keys)
    # For example, if you have a "posts" table with a "user_id" reference, add a "user_uuid" there, etc.

    # 3. Remove old primary key constraint
    remove_column :users, :id

    # 4. Rename 'uuid' to 'id'
    rename_column :users, :uuid, :id

    # 5. Set new primary key
    execute "ALTER TABLE users ADD PRIMARY KEY (id);"
  end

  def down
    # If you need to roll back, do the reverse steps
    # (This part can be a bit complicated to do properly)
  end
end
```

Now, once you run the migration it starts using uuid as your id (primary key).
