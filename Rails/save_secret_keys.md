# Save Secret Key Safely
In here it's learn how to save your secret keys in a secure way so that you won't end up exposing those sensitive keys.  
Consider you have integrated 3rd parties and they have secret keys for authentication(For example SendGrid API secret key). Storing them in the code is a bad practice because anyone with code access can see it and use it. So you need to store them in a secure place. Thank to rails it has a default place to store your secret keys.

When ever you create a project you will have two files named `credentials.yml.enc` and `master.key`. Here `credentials.yml.enc` has encripted data of all the keys that are stored. That file is ecripted using `master.key`. So make sure you are not pushing the master.key to any of the repository(gitignote this file if you are not doing it).  

You can edit the credentials file using the command
```bash
bin/rails credentials:edit
```
But you need to be specifing the editor it needs to use to edit. To use VS code to edit use
```bash
VISUAL="code --wait" bin/rails credentials:edit # For VS code
VISUAL="nano --wait" bin/rails credentials:edit # For nano
```

This will open the credentials file in a selected editor and you can edit it. File will look something like this.
```yml
secret_key_base: SOME_KEY
sendgrid:
  api_access_key: SENDGRID_ACCESS_KEY
```

Once you save the file, data will be stored in `credentials.yml.enc` in a encripted text. Now the question is How to access it.

You can use `Rails.application.credentials` which is a default in rails to access the credentials.

For example
```ruby
secret_key_base = Rails.application.credentials.secret_key_base
sendgrid_key = Rails.application.credentials.sendgrid[:api_access_key]
```

In development environment it works file. But what about in production. How to store the `master.key` file? Well that simple you copy the content present in `master.key` and past it as a environment varibale under the key **RAILS_MASTER_KEY**.

we are doing this because when the rails code runs it first check if any environment variable names RAILS_MASTER_KEY is present. If not then it checks if `config/master.key` is present. If those two are not present then it throws an error.

That's it. Happy coding.