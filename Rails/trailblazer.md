# Trailblazer

It is a architecture which is used to maintain a clear code and it helps to organize business logics, flows and data operations in a more structured way than the traditional.

First we need to get the bundles for trailblazer.

```ruby
gem 'trailblazer-rails'
```
Add the above line in your gemfile and do `bundle install`.

Now you can use trailblazer in your project.

Consider you have a business logic that is present in your controller. Over time it can become so complex and clumsy. So now using trailblazer you can keep the business logic in a seperate file in a well structured manner. In trailblazer we use something called steps. In each step we will be doing some business logics. If something fails in any of the steps, then trailblazer will return with some errors and it won't carry to next step.

Let's take an example.
Consider a scenario where you fetch the title and description from the user and save it in a posts table.

Before trailblazers code will be like:

```ruby
# app/controllers/post_controller.rb

def create
  @post = Post.new(create_post_params)
  @post.user = current_user
  if @post.save!
    # send a email for post creation
  end
end
```

After trailblazers, now you will be creating a new file under `app/concepts/post/create`(We store all the business logic under concepts).

```ruby
# /app/concepts/post/create.rb

class Post::Create < Trailblazer::Operation
  step :fetch_data
  step :save_post
  step :send_email

  def fetch_data(ctx, params:, **)
    ctx[create_post_params] = params.require(:post).permit(:title, :description)
  end

  def save_post(ctx, **)
    post = Post.new(ctx[:create_post_params])
    post.user = ctx[:current_user]
    post.save!
  end

  def send_email(ctx, **)
    # Sending email logics goes here
  end
end
```

If you see the above code, you can see there is something called `step:`, it is used to tell the trailblazers how the flow should be.
In each method we have 2 params default `(ctx, **)`.
**ctx** is known as context,tt stores all variables in a shared context, accessible across all steps in the operation. Keys are used to identify and retrieve these variables.
<strong>**</strong>,it collects all the extra keyword arguments passed to a method that are not explicitly defined in the method signature. It does not persist beyond that method unless manually stored somewhere(like ctx).

Now to run this trailblazer operation, in controller we need to call it.
```ruby
# app/controllers/post_controller.rb

def create
  Post.Create.call(params: params, current_user: current_user)
end
```

To know more on how to use Reform with Trailblazer [click here](./reform_with_trailblazer.md)