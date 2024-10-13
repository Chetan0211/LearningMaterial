# Use of Devise gem

In here we learn what is the use of devise gem and how to use it efficiently.

### What is the use of devise?
It is  basically authentication for your application. It is a simple but most powerful gem. You can do somany things like authentication, session management, user confirmations and lot more. Will look into it one by one.

### Installation Instructions

To install it we need to add the gem and all the dependencies. To add the gem in terminal we need to execute the following command.

```bash
bundle add devise
```
You will see something like `gem "devise", "~> 4.9"` in your gemfile.

Then you need to install the dependencies using the following command

```bash
rails generate devise:install
```

Once you run this command,  
it will also create two new files `config/initializers/devise.rb` which is used to setup devise and customize it and `config/locales/devise.en.yml` is used to customize the text that is shown in default templates of devise.
It will provide you with the instructions to setup some things manually if needed. It looks like below.
```bash
Depending on your application's configuration some manual setup may be required:

  1. Ensure you have defined default url options in your environments files. Here
     is an example of default_url_options appropriate for a development environment
     in config/environments/development.rb:

       config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }

     In production, :host should be set to the actual host of your application.

     * Required for all applications. *

  2. Ensure you have defined root_url to *something* in your config/routes.rb.
     For example:

       root to: "home#index"
     
     * Not required for API-only Applications *

  3. Ensure you have flash messages in app/views/layouts/application.html.erb.
     For example:

       <p class="notice"><%= notice %></p>
       <p class="alert"><%= alert %></p>

     * Not required for API-only Applications *

  4. You can copy Devise views (for customization) to your app by running:

       rails g devise:views
       
     * Not required *

===============================================================================
```


In this list just do the 1st point and others are optional(Over time will show you where we need to use those).

### Creating devise model

You can create devise model by using generate command.
```bash
rails generate devise user #Instead of user you need to specify your model name
```
Now this will either create the new model or it will update the existing model with same name if it is already exists. It will create new migration file which looks like below.

```ruby
# frozen_string_literal: true

class DeviseCreateUsers < ActiveRecord::Migration[7.1]
  def change
    create_table :users do |t|
      ## Database authenticatable
      t.string :email,              null: false, default: ""
      t.string :encrypted_password, null: false, default: ""

      ## Recoverable
      t.string   :reset_password_token
      t.datetime :reset_password_sent_at

      ## Rememberable
      t.datetime :remember_created_at

      ## Trackable
      # t.integer  :sign_in_count, default: 0, null: false
      # t.datetime :current_sign_in_at
      # t.datetime :last_sign_in_at
      # t.string   :current_sign_in_ip
      # t.string   :last_sign_in_ip

      ## Confirmable
      # t.string   :confirmation_token
      # t.datetime :confirmed_at
      # t.datetime :confirmation_sent_at
      # t.string   :unconfirmed_email # Only if using reconfirmable

      ## Lockable
      # t.integer  :failed_attempts, default: 0, null: false # Only if lock strategy is :failed_attempts
      # t.string   :unlock_token # Only if unlock strategy is :email or :both
      # t.datetime :locked_at


      t.timestamps null: false
    end

    add_index :users, :email,                unique: true
    add_index :users, :reset_password_token, unique: true
    # add_index :users, :confirmation_token,   unique: true
    # add_index :users, :unlock_token,         unique: true
  end
end

```

It will create a model file if not present and it looks something like this.

```ruby
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable, :trackable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :validatable
end

```

And it also adds `devise_for :users` in `routes.rb` file. So this will create routes for login, logout, signup, etc(Will see what are all the routes in details further).

Now, if you need to add more columns to the user model, you can do that in migration file or if you need to uncomment any devise specific operations you can do that. Once those are added you need to be doing the migration.
```bash
rails db:migrate
```

Once you migrate it restart the server and devise is added to that model.  

### Routes created by devise

`devise_for :users` will create some default routes.

#### Session Routes:
`new_user_session` (GET) — `/users/sign_in` (Login page)
`user_session` (POST) — `/users/sign_in` (Create session)
`destroy_user_session` (DELETE) — `/users/sign_out` (Log out)
#### Registration Routes:
`new_user_registration` (GET) — `/users/sign_up` (Sign-up page)
`user_registration` (POST) — `/users` (Register)
`edit_user_registration` (GET) — `/users/edit` (Edit user profile)
`user_registration` (PUT/PATCH) — `/users` (Update user profile)
`user_registration` (DELETE) — `/users` (Cancel account)
#### Password Routes:
`new_user_password` (GET) — `/users/password/new` (Forgot password)
`edit_user_password` (GET) — `/users/password/edit` (Password reset form)
`user_password` (POST) — `/users/password` (Send password reset email)
`user_password` (PUT) — `/users/password` (Update password)
#### Confirmation Routes:
(If you enable the :confirmable module)

`new_user_confirmation` (GET) — `/users/confirmation/new` (Resend confirmation instructions)
`user_confirmation` (POST) — `/users/confirmation` (Request confirmation instructions)
`user_confirmation` (GET) — `/users/confirmation` (Confirm account)
#### Unlock Routes:
(If you enable the :lockable module)

`new_user_unlock` (GET) — `/users/unlock/new` (Resend unlock instructions)
`user_unlock` (POST) — `/users/unlock` (Request unlock instructions)
`user_unlock` (GET) — `/users/unlock` (Unlock account)
#### Token Authentication Routes:
(If you enable the :token_authenticatable module)

`user_token` (POST) — `/users/token` (Generate token)

You can also Omniauth callbacks if you enable :omniauthable module and setup the omnuauth.

All these routes have default views. But if you want you can customize it.

### Customize the views

Even though devise give you default views which makes the job done, you need to customize the views at one point. To do that you can use `rails generate devise:views` in the terminal to view all the views and can customize them. But when you have multiple devise modals both uses the same view. But if you need two different views you need to first set `config.scoped_views = true` in `config/initializers/devise.rb` file.

Then you can specify which views you want to edit using `rails generate devise:views users`.

If you want to customize a particular module in the devise model, you can do that too like `rails generate devise:views users -v registrations confirmations`.

You can even customize even controller of devise model.
`rails generate devise:controllers users`
Remember, once you customize the controller that has to be specified in the routes.rb file.

```ruby
Rails.application.routes.draw do
  devise_for :users, controllers:{registrations: 'users/registrations'}
  #.. code goes here
end
```


NOTE: For more information on devise checkout [Devise Official Codebase and README file](https://github.com/heartcombo/devise?tab=readme-ov-file)