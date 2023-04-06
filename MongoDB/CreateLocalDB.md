# Create Local DB

**************Step1:************** Install mongosh in your system.

[NOTE: you can get the reference in following link: [Mongosh Installation Link](https://www.mongodb.com/docs/mongodb-shell/install/)]

[If you install MongoDB-community using brew in mac the to run MongoDB you need to set the mongosh path inside .zshrc.

And also run : brew services start mongodb-community this to run the mongo

]

Once you download MongoDB is set locally. You can use it by just typing

```bash
> mongosh
```

This will connect to the MongoDB [localhost](http://localhost) with default port 27017. Or you can specify in which port it needs to run

```bash
> mongosh "mongodb://localhost:27017"

// OR

> mongosh --port 27017
```

 

Once you create your local DB you can use [MongoDB Compass](https://www.mongodb.com/products/compass) (It is a GUI for visualising the MongoDB).