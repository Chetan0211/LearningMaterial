# How to parse body that is passed in POST API?

In POST API we can pass the body in it. It is not visible in route.

So to get access to the body we directly can't able to get the `req.body`. Before using that we need to parse the data.
To achieve this we need to add a package `body-parser`.

This can be added into the project using following commad in terminal

```bash
> npm install body-parser
```
After installing we need to import it.

```JS
var bodyParser = require('body-parser');
```
In body parser we have several formats like JSON, urlencoded, etc,.

For example, let's consider we are passing JSON in the body. Then we need to use JSON parser.

```JS
var jsonParser = bodyParser.json();
```

Now the `jsonParser` variable contains the middleware. This can be used in POST API where body is a JSON.

```JS
app.post('/api/postData', jsonParser,(req,res)=>{
    console.log(req.body);
    res.status(200).json({
        message: "working"
    });;
});
```

After using middleware now `req.body` contains JSON body
