# Initial Setup

Creating a node JS project is so easy.

First, you need to create a folder where you need to place the node project files. Once you create it, open the folder in the terminal and type the following command to create a package.json file.

```bash
> npm init
```

Once you type the command it will ask you the details like the name of the project, version, author, etc.

Once you create a package.json file, you can now create js files to create a node server.

Initially package.json looks something like this.

```json
{
  "name": "task_manager_backend",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node main.js"
  },
  "author": "Chetan",
  "license": "ISC",
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

In there as we can, we have all the details about the package, dependencies we used for the project, and scripts that have to run by typing the npm command. 

npm command like:

```bash
> npm start
```