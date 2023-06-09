# How to use Middlewares?

When user request to any API, before it reaching to that code it will be going through some set of codes, those are basically called middlewares.

For example you want to check if any particular request needs to check if it has permission to access. 
That check has to be made before even going to the main code. So, here we use middle ware to authorize if that request has access. In same way we use middleware for so many purposes.

In express there are many middleware functions.
One of it is `express.static(ROUTE)`.
Another middleware function is
`(request, response, next){}`

For example:

```JS
app.use('/api/temp',(request,response,next)=>{
  console.log('entered middleware');
  next();
});
```
here ROUTE is an optional parameter.
Here, we specified ROUTE as `/api/temp`, so any request that contains that API as initial will trigger that middleware.
HANDLER contains 3 parameters, `request`, `response` and `next`. 
Here we can access request and response values inside that function. To movie this request to next we need to call `next()` method, or else request won't move forward.

(**NOTE**: If you don't mention any ROUTE, then that middleware will run for all APIs)

You can also use middleware for GET, POST, PUT, DELETE, etc.


# Chain Middleware

we can chain the middleware and do some actions over there.

For Example:
```JS
app.get('/api/temp', (req,res,next)=>{
  req.time = new Date().toUTCString();
  next();
}, (req,res)=>{
  res.status(200).json({
      message: "working",
      time: req.time
  });
});
```

As you see for a GET request we used `(req,res,next)=>{}` first and followed by `(req, res)=>{}`.
So in middleware we get the time add added it in request object by creating a variable named time, and we accessed in the final Object`(req,res)=>{}`.

In the same way we can create as many middlewares we need.
