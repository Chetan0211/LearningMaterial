# Redis in Rails

So you will be having a question here like what is redis? and why we should use it? and how to install it?

### What is redis?

Simple and straight answer to it is Redis is an in-memory data structure store, used as a database, cache, and message broker. So you can store the data in an in-memory so you don't need to be hitting the database and it is much faster this way to access data.

### Why we should use it?

Consider you have a certain small amount of data where you will be needing that data to be accessed quite a lot of times. If you store that data in database and when you want to access that data each time it takes quite a lot of time to access which can be slower. But if you store that data in a in-memory like a cache data, this data is now stored in the server itself so you can access that data quite faster and also data can is durable.

We can store many different types of data like strings, hashes, list, sets, streams, etc. 

### How to install it and use it?

NOTE: Make sure you have installed Redis in your local system or any server your app is running and run the redis server.

First we need to install the Redis gem file, so add `gem 'redis'` in the gemfile and hit `bundle install` in terminal.

After that you need to create a file named `redis.rb` in `config/initializers` and add the following code.
```ruby
REDIS = Redis.new(url: "redis://localhost:6379/0" )
```
NOTE: In the URL part you have a number like 0 as above. That number represents the database it has to connect. So above you have connected to the database 0. You can specify each database for particular use.

Now you can use `REDIS` constant to set or get a data in redis server.

To set the data in redis:
```bash
REDIT.set("data", "Hello World!") # This will output as "OK" if it is saved
```

To get the data in redis:
```bash
REDIT.get("data") # This will output as "Hello World!"
```

Here you can also use connection pool along side with redis for better performance.

Connection pool is used to maintains a pool of active connections to a service(In here its a redis service). Creating connection for each request is time consuming so connection pool will create few connections and keep them alive. Now this connection pool will allow multiple threads to borrow the existing connections to make the request. Once the operation is done those threads will return the connection to the pool.

You need to be setting the connection pool in `config/initializers/redis.rb`
Setting the connection pool:
```ruby
REDIS_POOL = ConnectionPool.new(size: 5, timeout: 5) do
  Redis.new(url: "redis://localhost:6379/0")
end
```

As you see we are creating a new connection pool with particular size and timeout. Size specifies how many connections it needs to keep alive and timeout specifies that a thread can wait for specific time if all the connections are being used but will throw an error if that thread can't able to use any connection within that timespan.


To set the data:
```bash
REDIS_POOL.with do |conn|
  conn.set("data", "Hello World!") # This will output as "OK" if it is saved
end
```

To get the data:
```bash
REDIS_POOL.with do |conn|
  conn.get("data") # This will output as "Hello World!"
end
```