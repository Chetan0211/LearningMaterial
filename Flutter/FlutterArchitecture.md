# Flutter Architecture

Flutter Architecture mainly consists of 4 things:
1. Layers        2. Widgets        3. Concepts of State          4. Layers

### Layers:

Flutter framework is categorised based on the complexity and establishes the hierarchy based on decreasing level of these complexities.

At the Topmost layer is Platform Specific Widgets, these are written specific to Android, iOS, etc.

Second	layer will be Widgets, which are flutter widgets which consists of UI, gestures, state management components.

Third layer is Rendering, this is where the UI and state management get’s rendered.

Then, the layers go down to Gestures, foundation library, engine, and finally, core platform-specific code.

![image](https://user-images.githubusercontent.com/52970245/230426428-4c2f4419-a4c2-4307-b3a0-d2101f4a86f3.png)

![image](https://user-images.githubusercontent.com/52970245/230426558-e952f6f0-661f-4a82-9597-c517a1fb3d71.png)

### Widgets:

Widgets are the main components for the flutter. These widgets will act like UI for for the user and also will interact with the user. Flutter application itself is a widget which will be having number of different types of widgets.

MyApp will be the Top or Root level widget which will define the structure of the application.

MaterialApp is a widget there it contains all the properties of UI and the application is set.

Consider MyHomePage as a particular page.

Scaffold is one of the widgets that has visible component. It has 2 major properties namely AppBar and Body. AppBar consists of Heading. And in Body we can have all the component widgets(Child widgets).

![image](https://user-images.githubusercontent.com/52970245/230426610-2eaddc9b-d63d-447c-9ead-8269fc59a83a.png)

### **Gestures:**

Flutter contains pre-defined Gesture widgets that helps in interacting the application physically. Physical interactions includes tapping, swiping, dragging, etc.

### **Concept of State:**

The states are nothing but data objects. State helps in re-rendering the widgets specific to the state occurs and when ever the state changes. So instead of re rendering the whole application we are rendering the particular widget that is changed.
