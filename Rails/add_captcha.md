# Add CAPTCHA to rails application

We have different companies that provide CAPTCHA services. google reCAPTCHA, firebase reCAPTCHA, cloudflare, etc.

In here we are going to use cloudflare services.

First we need to create a free account in cloudflare. Once that process is done we can see a dashboard. In there click on `turnstile`which is a CAPTCHA service.

In that tab click on `add widget` (here widget is basically an application).
Specify the Widget name(application name), hostname management(here we need to specify the sites you need to activate) and click save it. Then it will be provided with `side key` and `secret key`.

Now in you application add `gem "rails_cloudflare_turnstile"` in you gemfile and do the `bundle install` in your terminal.

Once that is done we need to add the following script tag in `application.html.erb` file.

```html
# app/views/layouts/application.html.erb
<%= cloudflare_turnstile_script_tag %>
```

Let's take an example, you need to add the CAPTCHA in sign-in page, then in the controller we need to add `before_action`

```ruby
# app/controllers/users/sessions_controller.rb

class Users::SessionsController < Devise::SessionsController
  # before_action :configure_sign_in_params, only: [:create]
  before_action :validate_cloudflare_turnstile, only: [ :create ]

  rescue_from RailsCloudflareTurnstile::Forbidden do
    @user ||= User.new # Rebuild user object if needed for form redisplay
    flash.now[:alert] = "CAPTCHA verification failed. Please try again."
    render :new, status: :forbidden # Use 403 Forbidden status
  end

  # rest of the codebase below
end
```
Now in views we can add in two ways.
Adding `<%= cloudflare_turnstile %>`in your views.
Or you can do

```html
<div class="cf-turnstile"
  data-sitekey="<%= ENV['CLOUDFLARE_TURNSTILE_SITE_KEY'] %>"
  data-theme="light"
  data-size="flexible">
</div>
```

You also need to add `cloudflare_turnstile.rb` file in `/config/initializers`

```ruby
# /config/initializers/cloudflare_turnstile.rb

RailsCloudflareTurnstile.configure do |c|
  c.site_key = ENV["CLOUDFLARE_TURNSTILE_SITE_KEY"]
  c.secret_key = ENV["CLOUDFLARE_TURNSTILE_SECRET_KEY"]
  c.mock_enabled = false
  c.fail_open = true
  # Optional: Set to false to disable Turnstile verification entirely (useful for certain environments)
  c.enabled = true
end
```

That's it. Now when ever we go to sign-in page it should work now.

#### Errors found while developing

I've found an error while developing it. While we submit the singin and validation fails page reloads and can't findthe CAPTCHA. This is because of turbo load. When ever the page is loaded using tubro full page reload will not happen, as a result the script from `cloudflare_turnstile_script_tag` will not inject into the `cf-turnstile` class. As a result we can't able to load the turnstile.

###### Solution:
We need to create a stimulus file
```bash
bin/rails g stimulus turnstile
```

In there we need to add the following code.
```js
// app/javascript/controllers/turnstile_controller.js
import { Controller } from "@hotwired/stimulus"

// Connects to data-controller="turnstile"
export default class extends Controller {
  connect() {
    if (document.querySelector(".cf-turnstile")&& document.querySelector(".cf-turnstile").childElementCount == 0 && window.turnstile) {
      window.turnstile.render(".cf-turnstile");
    }
  }
}

```

Now we need to add the stimulus controller into views file.

```html
<div data-controller="turnstile">
  <div class="cf-turnstile mb-4"
    data-sitekey="<%= ENV['CLOUDFLARE_TURNSTILE_SITE_KEY'] %>"
    data-theme="light"
    data-size="flexible">
  </div>
</div>
```

That's it. Now it should work perfectly.
