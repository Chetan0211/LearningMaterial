# How to get parameter from Route

You want to pass some parameter inside the route so that we can do some actions by getting those parameters. For this to achieve, while we create an API we will be using `:`. For example: `/api/:parameterName/request`.

Now in the place of `parameterName` inside route you can pass the value while requesting to API and the value is stored with key as parameterName and that can be accessed using `req.params.parameterName`.

Example Route:

```JS
app.get('/api/:name/paramCheck', (req,res)=>{
    res.status(200).json({
        message: "working",
        name: req.params.name
    });
});
```
You can handle how many parameters you want in an API.
