# Deep Dive in main.dart file

Let’s see the basic code and know what is happening line by line.

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'First Application',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: const Text("Dummy Data"),
    );
  }
}
```

The first line is importing flutter’s material files which help in using flutter materials.
`void main()` is the place where the dart program starts to run. Inside that, we are using the default method named `runApp()` which helps in running the application in give native. We will be passing the main widget inside the runApp method.

MyApp is using StatelessWidget. So, we are overriding the build method which returns a `MaterialApp()`**which is like a starting point for UI**. Inside that, we will be specifying what has to be shown on the home screen. For now, we just printed the text. It will be looking something like below.

![image](https://user-images.githubusercontent.com/52970245/230427119-b121bc72-1b68-449b-a665-406791c3263e.png)

NOTE: We can do lot more in MaterialApp(). For example, we can specify the theme of the app here, we can specify the routes here using onRouteGenerator method. These are few of the examples.
