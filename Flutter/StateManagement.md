# State Management(Using Provider)

Consider a scenario where you want to maintain the state of a model and pass that state higher the widget tree. So to manage the state of the model under different widgets we use state management. We can achieve the state management using Provider package. First, install the package using below code.

```bash
flutter pub add provider
```

Now the biggest question is where you want to manage the state. Consider you have an application where it has 3 screens(or 3 widgets). HomeScreen, List items and Cart items.

image.png

Now, your ListItems screen and CartItem screen both uses the list of items and if you click on add to cart in ListItemScreen that should add an Item in the cart and that should reflect in CartPage. So you need to take the state up of the tree so that CartItem page can use up that state.

So you need to be creating the state at HomePage level.

image.png

For creating the state, first create a model. Here I created a model named HomePageModel.

```dart
import 'dart:collection';

import 'package:demo_app_2/MaterialApps/StateManagement/BOs/Item.dart';
import 'package:flutter/material.dart';

class HomePageModel with ChangeNotifier {
  //complete data
  final List<Item> _completedata = [];
  UnmodifiableListView<Item> get getCompleteData =>
      UnmodifiableListView(_completedata);

  void setCompleteData(List<Item> data) {
    _completedata.clear();
    _completedata.addAll(data);
  }

  void addToListData(Item value) {
    _completedata.add(value);
    notifyListeners();
  }

  //cart data
  final List<Item> _cartData = [];
  UnmodifiableListView<Item> get getCartData => UnmodifiableListView(_cartData);
  void setCartData(List<Item> data) {
    _cartData.clear();
    _cartData.addAll(data);
  }

  void addToCartData(Item value) {
    _cartData.add(value);
    notifyListeners();
  }

  void removeFromCartData(Item value) {
    _cartData.remove(value);
    notifyListeners();
  }

  void changeItemState(Item value) {
    if (_completedata.contains(value)) {
      var result = _completedata
          .singleWhere((element) => value.item_name == element.item_name);
      result.addedToCart = result.addedToCart ? false : true;
      notifyListeners();
    }
  }
}
```

In this model you can see I’ve used something like `notifyListeners()` method. This method is responsible to notify all the listeners that are present, so that when a change is occurred we call this method and all the. listeners gets this and change it’s UI accordingly.

We need to create a provider on top of the Widgets for notifying the changes. We will be either using the Provider widget to Create a provider or we can Use MultiProvider to create multiple providers.

Below we created ChangeNotifierProvider of HomePageModel.

```dart
Widget build(BuildContext context) {
    return MultiProvider(
      providers: [
        ChangeNotifierProvider(create: (context) => HomePageModel()),
      ],
      child: MaterialApp(
        debugShowCheckedModeBanner: false,
        home: HomePage(),
        onGenerateRoute: RouteGenerator.generateRoute,
      ),
    );
  }
```

Now your context contains the state of the model where you can access it using below code

```dart
context.read<HomePageModel>().WHAT_EVER_YOU_WANT_TO_ACCESS_IN_MODEL
```

If you want to listen to `notifyListeners()` to change the UI state inside UI you need to use below code

```dart
context.watch<HomePageModel>().WHAT_EVER_YOU_WANT_TO_ACCESS_IN_MODEL
```

When you use above code when we call notifyListeners(), watch will grab it and where ever we use that UI gets rendered.