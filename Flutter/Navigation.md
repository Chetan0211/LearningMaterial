# Navigation in Flutter

In any application, we need to be navigating the user to different screens in different scenarios. We can achieve this with navigation. In Flutter, navigation is also called routing. Screens or pages are called routes. 

In Flutter we use Navigator to navigate between routes.

We can navigate from one screen to another screen in 3 ways.

1. Anonymous Routing
2. Named Routing

### Anonymous Routing:

Here we just use an instance of the page directly while pushing a new page.

For example:

Consider there are 2 pages Page1 and Page2.

From Page1 you need to move the user to Page2. Then we use the following line of code to navigate.

```dart
Navigator.push(
    context,
    MaterialPageRoute(builder: (context) => const Page2()),
  );
```

In the same way, to Pop Page2 to show Page1 we use the following line of code.

```dart
Navigator.pop(context);
```

As you see above, we directly mention the page class [here it is Page2()]. The problem is we need to type the class name we want each and every time we want to navigate.

### Named Routing:

Here we are specifying the instances with the named routes in MaterialApp(), inside routes.

```dart
MaterialApp(
  title: 'Named Routes Demo',
  // Start the app with the "/" named route. In this case, the app starts
  // on the FirstScreen widget.
  initialRoute: '/',
  routes: {
    // When navigating to the "/" route, build the FirstScreen widget.
    '/': (context) => const FirstScreen(),
    // When navigating to the "/second" route, build the SecondScreen widget.
    '/second': (context) => const SecondScreen(),
  },
)
```

After specifying the routes, you can use those route names to navigate to that particular screen.

For navigating to the screen using named routing we will use the following code:

```dart
Navigator.pushNamed(context, '/second');
```

For popping the screen we use the same code that we used for anonymous routing.

But the main problem here is you can’t pass the parameters. But you can do that easily in anonymous routing

```dart
// Anonymous Routing
Navigator.push(
    context,
		// as you can see we can pass parameters to Page2 easily 
    MaterialPageRoute(builder: (context) => const Page2("parameter1", "parameter2")),
  );

// Named Routing

Navigator.pushNamed(context, '/second'); // Where to pass the parameters here?
```

So, to pass the parameters we have two ways to do it. Either use ModalRoute.of() or use onGenerateRoute() method.

Here I’m going to show how to use onGenerateRoute() as it is more simple and easy to use and maintain.

[If you need to know about ModalRoute.of() this is the link: [https://docs.flutter.dev/cookbook/navigation/navigate-with-arguments](https://docs.flutter.dev/cookbook/navigation/navigate-with-arguments)]

When you call Navigator.pushNamed(), onGenerateRoute() method gets called to check if that method is present. If present then that method gets called and it should return MaterialPageRoute() depending on the route name.

```dart
MaterialApp(
	initialRoute: '/'
	onGenerateRoute: (settings){
		// settings.name contains the named route it needs to navigate
		switch(settings.name){
			case '/second':
				// settings.arguments contains the arguments that have to be passed
				final args = settings.arguments;
				return MaterialPageRoute(
					builder: (context)=> Page2(args[0], args[1]);
				)
			break;
			default:
				return errorRoute();
			break;
		}
	}
);
```

So, when you want to push the second page with arguments then it will be pushed something like the below:

```dart
Navigator.of(context).pushNamed('/second', arguments: ['parameter1','parameter2'])
```

To make the code cleaner you can create a new class to handle the routes.

For example:

```dart
class RouteGenerator{
  static Route<dynamic> generateRoute(settings){
    switch(settings.name){
      case '/':
        return MaterialPageRoute(
          builder: (context)=> Page1()
          );
      break;
			case '/second':
        return MaterialPageRoute(
          builder: (context)=> Page2()
          );
      break;
      default:
        return MaterialPageRoute(
          builder: (context)=> errorRoute()
          );
    }
  }
}

class errorRoute extends StatelessWidget{
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    throw UnimplementedError();
  }

}
```

All the routes can be kept in one file and use the reference of generateRoute in MaterialApp()