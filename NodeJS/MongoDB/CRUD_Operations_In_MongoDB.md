# CRUD Operations using Mongo DB
----
### Insert a column inside Database table.

In [Create Table Model](https://github.com/Chetan0211/LearningMaterial/blob/main/NodeJS/MongoDB/CreateTableModel.md) we told how to create a Model for the specific table. We are going to use that table to add the data into it.

Now first we need to import the `DBModel` inside the file where you are going to use the model.

```JS
let dBModel = require('./Database/DBModel');
```

After that let's create an POST API call that gets you the required data. Once you got that data then you need to use `dBModel.TABLE_MODEL` to create a column with specified data. After that you need to save the data using `MODEL_INSTANCE.save()`. 

For example, this is how the code looks like:

```JS
app.post('/api/postData', jsonParser,(req,res)=>{
    let age = parseInt(req.body.age);
    
    // Changing Date from string to Date Object
    let dob = new Date(req.body.dob);
    
    // We are cerating a new instance of model and storing the data inside it.
    let user = dBModel.userModel({
        name : req.body.name,
        age : age,
        DOB : dob
    });
    
    // We are saving the data here.
    user.save()
    .then((data)=>{
        // If the data is saved successfully this part of code is executed
        res.status(200).json({
            message: "User Added"
        })
    })
    .catch(()=>{
        // If any error occures while saving data then this part of code is executed
        res.status(500).json({
            message: "Error occured"
        })
    });
});
```

This kinda approch is used to add a single data. If you need to add multiple data then you need to use `insertMany()` method which takes list of objects and insert those data.

For example, this is how the code looks like:

```JS
app.post('/api/postData', jsonParser,(req,res)=>{

    
    let finalArray = [];
    req.body.forEach(element => {
        let age = parseInt(element.age);
        let dob = new Date(element.dob);
        finalArray.push(dBModel.userModel({
            name: element.name,
            age: age,
            DOB: dob
        }))
    });
    
    dBModel.userModel.insertMany(finalArray)
    .then((data)=>{
        res.json({
            message:"Data Added"
        });
    })
    .catch((data)=>{
        res.json({
            message:"Error Occured"
        });
    });
});
```
----

### Read Data present in Database Table

You can use `.find()` method to get all the data from the table.

Example API code:

```JS
app.get('/api/getData', (req, res)=>{
    dBModel.userModel.find()
    .then((data)=>{
        res.status(200).json(data)
    })
    .catch((err)=>{
        res.status(500).json({
            message:"error"
        })
    });
});
```
The same `find()` can take conditions so that it will fetch the data according to that condition.

This is how we can give conditions:

```JS
dBModel.userModel.find({
        name: req.query.name
    })
    .then((data)=>{
        res.status(200).json(data)
    })
    .catch((err)=>{
        res.status(500).json({
            message:"error"
        })
    });
```
Return type for this will be list of data Object.

If you want to get only first data of the search, then you can use `findOne()` method.

Example code:

```JS
dBModel.userModel.findOne({
        age: 10
    })
    .then((data)=>{
        res.status(200).json(data)
    })
    .catch((err)=>{
        res.status(500).json({
            message:"error"
        })
    });
```
Return type for this will be data Object.


You can even find data using `findById()` method there you will pass the id the data. This id is created by mongo itself.

```JS
dBModel.userModel.findById(req.query.id)
    .then((data)=>{
        res.status(200).json(data)
    })
    .catch((err)=>{
        res.status(500).json({
            message:"error"
        })
    });
```

----

### Update Data present in Database Table

We can use `updateMany()` to update multiple data at a time. 
It takes 2 parameters, first parameter is search Object, second parameter is change Object. Then it will update all the data that have matched the search Object.

```JS
dBModel.userModel.updateMany({
        name: req.body.name,
    }, {
        age: parseInt(req.body.age)
    })
    .then((data)=>{
        res.status(200).json({
            message:"Data Updated"
        });
    })
    .catch((data)=>{
        res.status(500).json({
            message:"Error Occured"
        });
    });
```

In the same way with `updateOne()` to update a single data in the table. It also takes 2 arguments as `updateMany()` method.

----

### Delete Data present in Database Table

We can use `deleteOne()` to delete single data from the table.
It takes one parameter which is search Object to search.

Example for `deleteOne()` method:

```JS
app.delete('/api/deleteData', (req,res)=>{
    dBModel.userModel.deleteOne({
        name: req.query.name
    })
    .then((data)=>{
        res.status(200).json({
            message:"Data deleted"
        });
    })
    .catch((err)=>{
        res.status(500).json({
            message:"Error Occured"
        });
    });
});
```

Example for `deleteMany()` method:

```JS
dBModel.userModel.deleteMany({
        age: req.query.age
    })
    .then((data)=>{
        res.status(200).json({
            message:"Data deleted"
        });
    })
    .catch((err)=>{
        res.status(500).json({
            message:"Error Occured"
        });
    });
```
