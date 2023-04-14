# TextField Widget

If you need an text input field with single line you need to be using `TextField()` widget.

In this file I'll be problems faced and how to make better textfield UIs.

### Problems faced while using  `TextField()`
---

#### Unfocusing the TextField:

When a TextField is focused, when we click outside of the `Textfield()` it won't get un-focused. So, I found a solution for it.

There is a method `onTapOutside` inside `TextField` that will be triggred when you click outside.
You can use that with `focusNode` to unfocus the `TextField`.

```Dart
class DemoClass extends StatelessWidget{
// Create a new FocusNode
var textFieldFocusNode = new FocusNode();


  Widget Build(BuildContext context){
    return Container(
      child: TextField(
        // This method gets triggered when user clicks outside
        onTapOutside: (event){
          // Unfocusing the TextField
          textFieldFocusNode.unfocus();
        },
        focusNode: textFieldFocusNode,
      )
    );
  }
}
```
