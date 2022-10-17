# Intro to Scene Builder
Scene Builder is a JavaFX visual layout tool used for quickly building JavaFX UIs without coding. Scene Builder integrates with FXML files in your JavaFXML project. Within scene builder there are 3 main sections. On the left there  is `Library` and `Document` and on the right there is the `Inspector`.

# Library![Alt img >](SceneBuilder_Library.PNG)
This menu located on the left side of the screen contains all of the various UI Elements such as `Controls` and `Layouts`

## Controls
Any functional buttons or text entry fields are UI Controls. They are found in the `javafx.scene.control` package. Refer to [FXML Controls](FXML%20Controls.md) for more detail on unique controls that may require further explanation.

## Containers
Containers used to arrange and align Controls in a scene such as FlowPane, GridPane, BorderPane, AnchorPane, etc. They are locted in the `javafx.scene.layout` package. Some examples include:

- A `BorderPane` class is a layout that lays content out in the top, bottom, right, left or center region.
- The `StackPane`class places nodes from back-to-front in a single stack
- The `GridPane` class arranges nodes in a grid of rows and columns
- THe `FlowPane` class arranges nodes either horizontally or vertically with container wrapping or fitting.
- The `TilePlane` class arranges nodes in uniformly sized layout cells or tiles
- The `AnchorPane` class allows for placement to the top, bottom, left side, or center
- The `Hbox` class arranges nodes horizontally in a single row
- The `Vbox` class arranges nodes vertically in a single column

Layout can also be nested inside of other layouts. So you will often see something like a series of [UI Controls](#UI%20Controls) inside a `GridPane` inside a `Vbox`inside a `BorderPane` inside a `AnchorPane`.

# Document
This section located below the [Library](#Library) provides a view of the `Hierarchy` and the `Controller`which list the [FXID](JavaFX,%20It%20Has%20The%20Beans!.md#FXID)'s in the scene and what type of component they are. This is helpful for two reasons
1. You can select the specific components by clicking on the `fx:id`.
2. You can see if any component you select has an assigned `fx:id`.

# Inspector
The Inspector menu located on the right provides granular detail and settings for the selected object on the screen. There are three sections.
## Properties
Several of the following subcategories can be found here depending on the specific object some or all of the subcategories may appear here:
> #### Node
#PLACE-HOLDER-FOR-CONTENT  
> #### JavaFX CSS
> Style specific properties can be applied here as well as specifying Style Classes and Stylesheets
> #### Specific
> Usually features specific to the type of object. Such as [Toggle Group](JavaFX,%20It%20Has%20The%20Beans!.md#Toggle%20Groups) for radio buttons.
> #### Extras
> #PLACE-HOLDER-FOR-CONTENT 

^42fced
## Layout
Too many subcategories to define #PLACE-HOLDER-FOR-CONTENT holder for any categories of interest.
## Code
Several of the following subcategories can be found here:
> #### Identity
> This is where the [FXID](JavaFX,%20It%20Has%20The%20Beans!.md#FXID) is defined.
> #### Main
> This is where the ID for On Action is defined
> #### DragDrop
> IDs for actions related to various Drag events can be defined here
> #### Keyboard
> IDs for actions related to various Keypress events can be defined here.

# Skeleton View
One nice feature in Scene Builder upon creation of your layout you can go to the menu bar at the top and select **View**-->**Show Sample Controller Skeleton**. Inside the Skeleton View all the various classes matching the FXML file and [@FXML annotations](JavaFX,%20It%20Has%20The%20Beans!.md#^f284d0) from your scene are written and provided for you.

