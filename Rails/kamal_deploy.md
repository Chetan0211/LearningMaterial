# Deploy rails application using kamal

In here we will learn on how to deploy rails application using kamal. 
We will be deploying the application in our personal server and create CI/CD pipeline to it.

In here we will be using our karma_loop application to deploy.

So, first we need to install kamal into our development system using below command.
```bash
> gem install kamal
```

Once kamal in installed then go to your codebase folder and do `kamal init` and this will create a file in `./config/deploy.yml`.

