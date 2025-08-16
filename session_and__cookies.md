# Session Storage and cookies

In here we are going to learn about difference between session storage and  cookie storage in web browsers.

## Session Storage

Any data stored in a session storage will be only present until tab that is used to create is present. Once that tab is closed then all the session storage will be cleared.

Cosider you have created a session data for your application in a specific tab, then you can only access that data from that tab alone, if you open another tab with the same application you cannot access the session data created in another tab. So basically sessionData is specific to each tab not the application.

We can store the data into session storage using key value.

```javascript
//window.sessionStorage.setItem('KEY', 'VALUE')
window.sessionStorage.setItem('somedata', 'xyz_data')
```

We can fetch the data from session storage using the key.

```javascript
//window.sessionStorage.setItem('KEY', 'VALUE')
window.sessionStorage.getItem('somedata')  //Output: xyz_data
```


## Cookie Storage

Cookies on the other hand are small pieces of data that web sites store data in a web browser. These are the main reson for websites to remember information about users and their interactions.

#### How it works:
Server sends the cookie --> Browser stores cookies --> Browser sends the cookie back

**Server sends the cookie**: When we visit a website, the web server can send a set-cookie HTTP header in its response to your browser. This header contains the cookie's name, value, and various attributes like expiration date, domain, path, and security flags.

**Browser Stores Cookie**: Your browser receives this instruction and stores the cookie on your device, typically in a dedicated file.

**Browser Sends Cookie Back**: For every subsequent request your browser makes to the same domain (or a matching domain and path), it automatically includes the relevant cookies in the HTTP request headers. This is how the server "remembers" you and your previous interactions.

There are basically two types of cookies:

**Session Cookies(Non-persistent cookies)**: hese cookies are temporary and are designed to last only for the duration of your browsing session. They are deleted as soon as you close your browser. They are often used for managing user activity within a single visit, such as keeping items in a shopping cart as you navigate an e-commerce site.

**Persistent Cookies**: These cookies have an expiration date set by the server and can remain on your device for a specified period (hours, days, months, or even years). They are used to remember you across multiple visits, allowing websites to personalize your experience, remember your login status (like the "remember me" feature in Devise), or recall your preferences.

We can able to set and get the cookies from client side too, but it is not intended for that purpose and when server is set it as httpsonly then client side JS cannot get the data from that cookie.

You can fetch and get the cookie data from JS in following way.

```javascript
// To fetch the cookie
document.cookie

//TO set the cookie
document.cookie = "username=JohnDDoe, city=SomeCity"
```

Now we will Look into how we can set the cookies from server side(Here i'll be using rails to show an example)

```ruby
# To set session cookie
cookies[:username] = "JohnDoe"
cookies[:city] = "someCity"

# TO set permanent cookie
cookies.permanent[:username] = "JohnDoe"
cookies.permanent[:city] = "someCity"
```

we can also set options to a cookie like expires, domain, path etc.

```ruby
cookies[:theme] = {
  value: "dark",
  expires: 1.month.from_now,  # cookie expires in one month
  domain: :all,               # Available accross all subdomains
  path: "/",                  # Available accross all paths
  secure: true,               # Only sent over https
  httponly: true,             # Not accessable via client-side JS
  same_site: :lax             # Helps prevent CSRF attacks
}
```

In rails we can also have signed and encrypted cookies using `cookies.signed` and `cookies.encrypted`.

