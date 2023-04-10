# First API call using Express

So first we need to add the Express package as told in previous file ([AddingExpressJS.md](https://github.com/Chetan0211/LearningMaterial/edit/main/NodeJS/AddingExpressJS.md))

Now we need to import the package to the js file.

```JS
  var express = require('express');
```

Now we need to run the express by adding the following line.

```JS
  let app = express();
```

Now we can add API using the express instance(here it is stored in app variable).
For example lets create an API with GET request that gives a JSON as response.

```JS
  app.get('/api/temp', function(req,res){
    res.status(200).json({
        message: "working"
    });
  });
```
After creating, you need to keep listening to the request. You can do that using following code
```JS
  app.listen(8090, ()=>{

  });
```

Now when you run the Node app, it will start listening to request at port 8090.

Now if you call `http://localhost:8090/api/temp` API, you will get the JSON response as output with 200 status.

```JSON
{
    "message": "working"
}
```
