# BuildContext in flutter

BuildContext is mainly used to know the location of the widget in the widget tree.

Each widget contains it’s own BuildContext. It contains all the information about that widget like size occupied by that widget, HashCode of that widget, runtime type of the widget, etc.

You can do so many things using this build context. We can refer to the parent elements using it and vice versa.

Let’s take an example to understand it clearly:

Consider you want to know the size(width/height) of a scaffold. 

```dart
@override
Widget build(BuildContext context){
	return Scaffold(
		body: ElevatedButton(
			onPressed: () {
	      print(context.size);
      },
			child: Text("click me"),
		),
	);
}
```

So, when you press the button you will get the size of scaffold that is occupied.