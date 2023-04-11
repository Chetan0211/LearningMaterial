# How to get query parameters from Route

You can pass parameters in API after `?`. For example: `/api/route?queryName=Value&queryName2=Value2`.

This can be accessed using `req.query.queryName`

Example Code:

```JS
app.get('/api/queryCheck', (req,res)=>{
    console.log(typeof(req.query.name));
    res.status(200).json({
        message: "working",
        name: req.query.name
    });
});
```
So now when you call the API `api/queryCheck?name=chetan`, you can get the query value using `req.query.name`.
