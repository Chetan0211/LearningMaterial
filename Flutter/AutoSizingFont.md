# Auto sizing the font according to screen size.

The main problem we face while designing the UI is how to keep the font size dynamic according to the screen size. 
You can achieve this efficiently in 2 simple ways. 

### Using `FittedBox()`

You can wrap the text using `FittedBox()`, so if the text is overflowing it will reduce the size of the text automatically so that it won't look awkward in any of the devices.

Example:

```Dart
FittedBox(
  child: Text(
    "This is a text",
    style: TextStyle(
      color: Color.fromRGBO(202, 199, 237, 0.635),
      fontSize: 60,
      fontWeight: FontWeight.w600,
    )
  )
)
```

### Using `AutoSizeText()`

To use AutoSizeText you need to install the package first [auto_size_text](https://pub.dev/packages/auto_size_text). After installing you can use it to change the text size.
You can specify the minimum and maximum screen sizes to it.

Example:

```Dart
AutoSizeText(
  'This is some text',
  minFontSize: 16,
  maxFontSize: 28,
  overflow: TextOverflow.ellipsis,
)
```


These above two approches are easy once. You can also use layout builder to get the height and width and give the size accordingly.

```Dart
LayoutBuilder(
  builder: (BuildContext context, BoxConstraints constraints) {
    return Text(
      'This is some text',
      style: TextStyle(fontSize: constraints.maxHeight / 10),
    );
  },
)
```
