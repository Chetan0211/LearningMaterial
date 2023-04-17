# Create Table Model for MongoDB table

After connecting to the database you need to create a model so that you can use that model to do all the CRUD operations into the database.

Let explain this with an example.

Consider you need to create an user model where user table consists of 3 columns name(String), age(Number) and DOB(Date).
For that we need to create a new file named `DBModel.js` and inside that import DBInstance(so that DB instance get initialized).

Create new Schema using `Mongoose.schema()`. Once you create schema you use `Mongoose.model()` to create model for that table schema and export that userModel

```JS
let mongoose = require('mongoose');
let dbInstance = require('./DBInstance');

let userSchema = mongoose.Schema({
    name: {
        type: String,
        unique: true,
        require: true,
        validate: (value)=>{
            return (value != null || value != "");
        }
    },
    age: Number,
    DOB: Date
});

// We are either creating or fetching the table with name as UserTable and has userSchema.
let UserModel = mongoose.model('UserTable', userSchema);

exports.userModel = UserModel;
```

As you see, in Schema we need to mention the column name and type it holds. As you see in example is a column has multiple parameters to set, then we use object where we specify all the parameters. For example some of the parameters are: if the column has unique values, is that column required, we can also specify the validations to it.

Once the Schema is done, we need to create a model using that schema with the table name.


For more refer:[Mongoose schema types](https://mongoosejs.com/docs/schematypes.html)