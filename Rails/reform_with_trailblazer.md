# Reform with trailblazer

Reform is a gem commonly used with Ruby on Rails to manage form validations, coercions, and nested object handling outside of ActiveRecord. It decouples the form logic from models and views, making it an excellent choice for complex form handling.

The main advantages of Reform are:

1. **Decouple from logic:** Keeps your model clean by separating form-related logic and validations from the model layer
2. **Centralized validation:** All form-specific validations are placed in a single location, improving readability and maintainability.
3. **Works with Non-ActiveRecord Objects:** Reform works with any Ruby object, not just ActiveRecord models.
4. **Supports nested forms:** Handles complex forms with nested attributes easily.
5. **Easy integration with Trailblazer:** Seamlessly integrates with Trailblazer operations, ensuring a consistent flow of business logic and form handling.
6. **Coercion of Data Types:** Reform can coerce incoming form data into specific types, ensuring consistency before further processing.

For example, converting string to dataform.
7. **Simplifies Complex Forms:** Reduces boilerplate code in controllers for handling multi-model or multi-step forms.
8. **Better Error Management:** Provides detailed error handling and feedback mechanisms for forms.
9. **Reusability:** Form contracts can be reused across different parts of the application (e.g., API forms vs. web forms).
10. **Extensibility:** Reform can be extended to add custom validation methods, hooks, and coercion logic as per application requirements.


Let's take an example on how to use the Reform for a form validation and saving data into database.

Scenario: When we user likes or dislikes the post then it should be saved in the database.

For this first we need to create a reform validation.

```ruby
# app/concepts/post/contract/post_reaction.rb

class Post::Contract::PostReaction < Reform::Form
  property :post_id
  property :user_id
  property :reaction

  validates :post_id, presence: true
  validates :user_id, presence: true
  validates :reaction, presence: true, inclusion: { in: %w[like dislike] }
end
```

Now we can use this contract to validate the form data.

```ruby
# app/concepts/post/post_reaction.rb

class Post::PostReaction < Trailblazer::Operation
  step :setup_model
  step Contract::Build(constant: Post::Contract::PostReaction)
  step Contract::Validate()
  step Contract::Persist()

  def setup_model(options, posts_reaction:, **)
    options[:model] = PostsReaction.find_or_initialize_by(
      post_id: posts_reaction[:post_id],
      user_id: posts_reaction[:user_id]
    )
    options[:model].reaction = posts_reaction[:reaction]
  end
end
```

Here you can see we are storing the model in a `options[:model]` because, when ever we validate the data using Reform, it always looks for the model key and validate the data present inside the model key. So that is the reason for the 1st step in trailblazer. In the next step we are building the contract and we validate the data using that contract. At last we use persist() method that is used to save data into DB.


