# Mixins

These are the classes with methods and properties which are used by another classes.

Let’s take an example:

Consider you have a class named Parent1 which uses a method Action1 to print a string. In the same way we have class named Parent2 which used a method Action2 to print a string. Now another class named Child wants to use both the method Action1 and Action2. But the problem here is we cannot have multiple inheritance for a class.

```dart
class Parent1{
  void Action1(){
    print("This is action 1");
  }
}

class Parent2{
  void Action2(){
    print("This ia action 2");
  }
}

// We cannot do this as dart do not allow multiple inheritance
class Child extends Parent1, Parent2{
  void ChildAction(){
    Action1();
  }
}
```

And if we write the same method here then we are making the code duplicates in our project. So to reduce that we can use something called Mixins.

We can create a mixin and add the method over there and that can he used in any class by using that mixins.

Now consider, Parent1 and Parent2 are mixins instead of class. Now we can use multiple mixins into the child using with keyword

```dart
void main() {
  var c = new Child();
  c.ChildAction();
}

mixin Parent1{
  void Action1(){
    print("This is action 1");
  }
}

mixin Parent2{
  void Action2(){
    print("This ia action 2");
  }
}

// We add Parent1 and Parent2
class Child with Parent1, Parent2{
  void ChildAction(){
		// from Parent1
    Action1();
		// from Parent2
    Action2();
  }
}
```

We can specify which type or class can include the mixin. Once you specify, then we can’t include the mixin if the it is not the type or child type.

For example, consider you have a class Named SubChild class which extends Child class. Now consider you added type Child to Parent1.

```dart
mixin Parent1 on Child{
  void Action1(){
    print("This is action 1");
  }
}
```

Now, when any class that inherits Child class or their children can you the Parent1 mixin. Other than those we can’t use Parent1 minix on any other classes.

```dart
// We can't do this as Parent1 can only use to children that inherits Child
class AnotherChild with Parent1{
}
```

Correct way to do it:

```dart
void main() {
  var c = new SubChild();
  c.mainAction();
}

mixin Parent1 on Child{
  void Action1(){
    print("This is action 1");
  }
}

mixin Parent2{
  void Action2(){
    print("This ia action 2");
  }
}

class Child with Parent2{
  void ChildAction(){
    Action2();
  }
}

//It extends Child so it can use Parent1
class SubChild extends Child with Parent1{
  void mainAction(){
    Action1();
    ChildAction();
  }
}
```
