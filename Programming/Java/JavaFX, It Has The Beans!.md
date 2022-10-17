_******* Disclaimer *******_ This document references the use of JAVAFX employing [MVC](../MVC.md) architecture. All information below are focused on that design principle

# JavaFX Packages
JavaFX elements and controls are broken down into packages. These packages will have the prefix `javafx` and will look like this:

```java
javafx.stage
javafx.application
javafx.scene
```

# JavaFX Application Structure
As explained in the section on classes in the [1-Java needs GFuel - Start](1-Java%20needs%20GFuel%20-%20Start.md##Classes) document JAVA is all about [OOP](../An%20Introduction%20to%20Programming.md).

![Image alt ><](javafx_application_structure.jpg)
## Stage and Scenes
Think of the `stage` as the back end of the computer program. You don't actually see it, but its what initializes the `scene`.

Think of the `scene` as the main application window. All objects sit inside a scene. All Java applications must have at least one `stage` and one `scene`

## Nodes and Scene Graphs
`Nodes` are essential objects such a buttons or text fields. They can contain a group of `nodes` such as bounding box. 

A `Scene Graph` contains a group of nodes as well. It has one primary node or `root node` that all leaf nodes steam from.  Parent Nodes are called `Branch Nodes` and children nodes that contain no other children are called `leaves.` 

## Layouts
These are used to arrange elements or controls in a scene. Some layout classes for example are `Anchor Pane` and `Grid Pane`. See the [layout](Scene%20Builder.md#Layouts) section in the [Scene Builder](Scene%20Builder.md) document for more examples and explanations.

# Application Class and Life-Cycle Methods
JavaFX application is a subclass of the `Application` class which is located in `javafx.application` package.
The application class defines only three life-cycle methods: `init()`, `start()`, and `stop()` and are called in that specific order. 
## init()
This is the method that is called when the JavaFX application is first launched. It can't be used to create a [`stage` or `scene`](JavaFX,%20It%20Has%20The%20Beans!.md##Stage%20and%20Scenes) [Overriding](1-Java%20needs%20GFuel%20-%20Start.md#Overriding) the init() method is not required as an empty default is already included.
## start()
This method is called directly after [init()](##Init()) and is used to Construct and set Scenes when your JavaFX application starts. It references a [Stage](##Stage%20and%20Scenes) object as a parameter. This `Stage` object is provided at runtime, and is the Primary Stage for your JavaFX application. Because stat() is an [abstract](An%20Introduction%20to%20Programming.md#Abstraction) method part of an [abstract class](1-Java%20needs%20GFuel%20-%20Start.md##Abstract%20Classes), so it can't be called, only [overriden](1-Java%20needs%20GFuel%20-%20Start.md#overriding).
## stop()
Rather self-explanatory, but essentially it is the method that is called when a JavaFX application is terminated. Used for clean up operations etc. Because it includes an empty default it is not required for the program to run successfully.
## launch()
JavaFX applications are launched by the launch() method. This method calls the [init() and start()](#Application%20Class%20and%20Life-Cycle%20Methods) methods and is returned when the JavaFX Application is closed at which point the [stop()](##stop()) method is called.

# JavaFX Application code example
This example is a basic launching of a JavaFX application that has a button that says hello world. All the parameters and methods necessary to start the application are defined in this example.

```java

package mwjavafxapplication;

import javafx.application.Application;
import javafx.event.ActionEvent;
import javafx.event.EventHandler;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.layout.StackPane;
import javafx.stage.Stage;

public class MWJavaFXApplication exends Application {

	@Override
	public void start(Stage primaryStage) {
		Button btn = new Button()
		btn.setText ("Say 'Hello World!'")}
		btn.setOnAction(new EventHandler<ActionEvent>() {

			@Override
			public void handle(ActionEvent event) {
				System.out.println("Hello World!");
			}
		});
		StackPane root = new StackPane();
		root.getChildren().add(btn);

		Scene scene = new Scene (root, 300, 250);

		primaryStage.setTitle("Hello World!");
		primaryStage.setScene(scene);
		primaryStage.show();
	}

	/**
	* @parm args the commandline arguments
	*/
	public static void main(String[] args) {
		launch(args);
	}
}
```

^38cadb

Notice that [init()](##init()) and [stop()](##stop()) were not defined in the example above? That is because again they are *optional* methods.

# JavaFX Controller
JavaFX controller works based on [MVC(Model-View-Controller)](../MVC.md). The controller's main purpose is to be the intermediary between the View (UI) and the Main (Compiled Program). In the controller, the UI elements in the various [View](MVC.md#View) Classes are identified using those object's [FXID](#FXID). Any [Event Handlers](#Event%20Handler) are defined here as well. The controller class is used to *pass the butter*.


![image alt ><](rick-and-morty-pass-the-butter.gif) 													  

One of the most frustrating aspects of Java is that since everything is encapsulated in classes there are no global variables or objects. The closest you get is to make an object or a method [Static](1-Java%20needs%20GFuel%20-%20Start.md#Static). They still aren't global it just allows a class to [instantiate](An%20Introduction%20to%20Programming.md#Instantiation) an object or method with in the class that can be accessed somewhere else by calling the class and that particular method or object. Its kind of like putting a plastic tube through concrete. The static methods/objects are the tube material that support the hole in the tube to allow allows access in and out of the class that is made out of concrete.

JavaFX MVC can be achieved by FXML (EFF-ects eXtended Markup Language). FXML is an XML based language used to develop the graphical user interfaces for JavaFX applications as in the HTML. FXML allows developers to separate User Interface logic from the Business logic. If suppose the [User Interface in your JavaFX application](https://www.educba.com/javafx-applications/) needed to change, then there would be no need to compile the application. If we want, we can edit the FXML in the editor and re-run the app. 

# Controller Caveat
A controller dies and is reinitialized every time you switch screens that call it. So if you have written code under the ``initialize`` method that code will execute every time the controller is called not just when the screen is first loaded.

# FXID
An FXID is a unique identifier used for referencing a Java FXML UI control in a Controller. They are initialized in the FXML by assigning a value to the `fx:id` attribute. In [Scene Builder](Scene%20Builder.md) the Inspector panel on the right is where an fx:id can be applied. 

#GoodCodingPractice 
`@FXML` is an annotation similar to [`@Override`](1-Java%20needs%20GFuel%20-%20Start.md#^6c58e6) to insure that the declared field that follows, has a matching FXID. A complete @FXML annotation statement includes a `private` Access Specifier, a UI Control `Reference Type`, and a `reference variable` declared using the same name as the UI Control's FXID. Using a dereferenced FXID in a [Controller](#JavaFX%20Controller) causes a Null Pointer Exception. Its basically saying, *"HEY! I can't find what your looking for here!"* ^f284d0

# Events and Event Handling
A brief definition is provided here in [An Introduction to Programming](An%20Introduction%20to%20Programming.md#Events).
There are several components within an event.
### Event Source
The component that generated the Event e.g. a button, a radio button, a checkbox, etc
### Event Object
An Event Object contains information about the event. These objects are created when the event is triggered.
### Event Listener
An Event Listener is a Class that contains one or more methods (handlers) that respond to Events. 
### Event Handler
An Event Handler is a method that contains instructions that are executed when the event occurs.

Event handlers are declared the same way FXIDs are:
```java
package controller;
import javafx.fxml.FXML;
import javafx.scene.control.Button;
public class MainMenuController implements Initializable{
	@FXML
	private Button createbtn; 
	/* 
	The On Action ID located in the code heading directly
	under FX:ID "createBtn" in Scene Builder has been 
	defined as "onActionCreateButton" The naming convention
	"onAction" is to remember what type of method is called
	and "CreateButton" is the name of the button.
	These are not a requiremnent just a naming convention.
	*/

	@FXML
	public void onActionCreateButton(ActionEvent event) {
		//Code to do something like switch screens goes here
	}
}
```
Remember the purpose of this is to define the Event Handler with a name for each action. So we can specify what to do when the action occurs. This is the main purpose of the [JavaFX Controller](#JavaFX%20Controller)

# Toggle Groups
A Toggle Group ensures that out of a group of Radio Buttons, only one can be selected at a time. They are specified in the [Properties](Scene%20Builder.md#Properties) section in Scene Builder using a unique String located in the [Specific subcategory](Scene%20Builder.md#^42fced)

# FXMLLoader
FXMLLoader is a class that provides members for working with [Views](MVC.md#View) and their [Controllers](MVC.md#Controller). FXML Loader method types are: `load()`, `setLocation()`, `getRoot()`, and `getController()`
## Example of the FXML Loader using an Instance Method.
``` java
FXMLLoader fxmlLoader = new FXMLLoader();//creating FXMLLoader object  
fxmlLoader.setLocation(new URL("path/fileName.fxml"));//accessing FXML file	
```
## load()
This method loads resources from a project directory to an application. It is an [overloaded](1-Java%20needs%20GFuel%20-%20Start.md#Overloading) method with a variation that can accept the name of an FXML document including its location / path, as a String

## setLocation()
This method allows you to select the location path and file of the FXML view to be loaded.  It isn't entirely necessary as you could specify the location in the load method.

## FXMLLoader Further Defined
There are several different ways to use the FXMLLoader object refer here to a better example of why and how to use the different methods: https://edencoding.com/fxmlloader/


# ObservableArrayList()
The `observableArrayList()` is actually an object similar to an Array List. Even though it is written like a method it is actually an object



# Additional Sources of Information

## [Better Example of MVC in JavaFX](https://edencoding.com/mvc-in-javafx/)