# How to create Mongoose DB Instance?

So, first we need to connect the mongoDB database in NodeJS.
For that we need to use the following command:

```JS
mongoose.connect(CONNECTION_STRING)
```
We can use `.then()` and `.catch()` to check if DB is connecrted or not.

For example, we have created a new file named `DBInstance.js` file and inside that we connect the DB.

```JS
var mongoose = require('mongoose');

class Database{
    constructor(){
        mongoose.connect('mongodb://localhost:27017/')
        .then(()=>{
            console.log("Connected");
        })
        .catch(()=>{
            console.log("Error Occured");
        });
    }
}

module.exports = new Database();
```
This is how we connect to MongoDB.
