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

But the problem we face here is, `onTapOutside` will trigger everytime we click outside of the textfield, and also it will never trigger if you click on another textfield. 
This is a huge drawback of the above code. 
Cosider a scenario where you want to change the UI state according to focus. We can't achieve it with above code. So, to schieve that we need to use the `FocusNode` again, but in a different way.
We need to add a listener to the FocusNode , so that any changes in the focus get's notified. So, depending on that you can actually do the actions.

```Dart
class _CustomTextFieldState extends State<CustomTextField> {
  var focusNode = new FocusNode();
  @override
  void initState(){
    focusNode.addListener(() {
      if(!focusNode.hasFocus){
        //Triggers when textfield lost focus
        // Do what ever the action you needed
      }
    });
  }
  
  Widget build(BuildContext context) {
    return TextField(
      focusNode: focusNode,
      .
      .
      .
    )
  }
}
```

Using the above code we can unfocus the textfield properly without any issues.
