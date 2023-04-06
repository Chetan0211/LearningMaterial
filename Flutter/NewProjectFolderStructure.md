# New Flutter Project And Its Folder Structure

In the terminal type the following code

```bash
> flutter create {APPLICATION_NAME}
```

Example: flutter create first_application

Now the application is created with the given name in the specified path. A basic application is created which can be run on iOS, Android, and even on the web.

The default folder structure will be something like below.

image.png

In lib folder there will be main.dart file. This is the place where our application code starts.

Let’s concentrate on mobile native once for now.

Inside android→app we can see src folder, inside that we can see AndroidManifest.xml file. It will be very useful(Will be explained more in the future).

Inside ios→Runner we can see info.plist file. It is also very useful file(Will be explained more in the future).