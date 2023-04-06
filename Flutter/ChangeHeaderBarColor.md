# Change the Header bar color of the System(Android/iOS)

To change the color of header text of system can be change between light and dark using the following code.

```dart
AnnotatedRegion<SystemUiOverlayStyle>(
      value: SystemUiOverlayStyle.dark,
      child: ...//Child goes here.
)
```

When you don’t use `AppBar()` as a header then the above code is used. Inside the child we will be keeping the main widget (in this case it will be Scaffold).