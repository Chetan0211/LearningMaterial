# CRUD Operations using Mongo DB
----
### Insert a column inside userdatas table.

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
        let age = parseInt(req.body.age);
        let dob = new Date(req.body.dob);
        finalArray.push(dBModel.userModel({
            name: element.name,
            age: element.age,
            dob: element.dob
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
