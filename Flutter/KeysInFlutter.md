# Keys in Flutter

Key in a widget stores the state even when device moves from one branch to another in a widget tree. Keys are attached to the widget elements, making them distinguishable in the element tree even if they have the same widgets.

We use keys where multiple widgets of the same type are present at the same level of the widget tree. We declare the key at the top of the branch of the widget tree whose states need to preserve.

A key can be a value, Object, Unique or Global key.

**Value Key:** If a key contain one alpha numeric value for a widget.

********************Object Key:******************** If a key contain Objects as a key(mostly custom class Objects) which can store multiple data like Username, Age, etc.

********************Unique Key:******************** If a key contains a unique value to represent a particular widget. This is used mostly when we has same type of widget with different values and we have to access them using those unique values.

********************global Key:******************** If a key is stored globally to maintain the state of the widget we use this kind of keys. But it is mostly not advisable as we have another type of state management which are more effective.

For more info refer this link: [https://api.flutter.dev/flutter/foundation/Key-class.html](https://api.flutter.dev/flutter/foundation/Key-class.html)

Video link: [https://youtu.be/kn0EOS-ZiIc](https://youtu.be/kn0EOS-ZiIc)