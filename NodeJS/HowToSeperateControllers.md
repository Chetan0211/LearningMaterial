# How to sepertate controllers to different files

Until now we are keeping all the controller in one place. But once the application gets larger it is very difficult to keep the whole code in one place. So, it's better to keep the controllers in different files according to their purpose.

First we need to create a folder with the particular controller name (For example lets create a folder called `UserController`).
Inside that method create 2 files one is to maintain routes and another to maintain controller methods.(For example 2 files will be `UserControllerRoutes.js` and `UserControllerMethods.js`)

Before looking into the router file, let's look into Methods file. We need to create a class and inside that we need to write all the methods which are used in route file and export the class.

For Example:

```JS
let dBModel = require('../Database/DBModel');

class UserController{
    FetchUserData(req, res){
        try{
            dBModel.userModel.findById(req.query.id)
            .then((data)=>{
                res.status(200).json(data)
            })
            .catch((err)=>{
                res.status(500).json({
                    message:"DB Exception"
                })
            });
        }
        catch{
            res.status(500).json({
                message:"Internal Server Error"
            });
        }
    }
}

module.exports = new UserController();
```

Now, Inside the controller route file we need to create all the routes using `express.Router()`. We use Router to create new routes and export that Router.

For Example:

```JS
var express = require('express');
let userController = require('./UserControllerMethods');

let router = express.Router();


router.get('/api/getData', userController.FetchUserData);

module.exports = router;

```

Once the Router files are created, this files has to be added in main file.(Here main file is `main.js`).

```JS
var express = require('express');
let app = express();
let userController = require('./UserController/UserControllerRoutes');

app.use(userController);
```
Now those routes are ready to use.
