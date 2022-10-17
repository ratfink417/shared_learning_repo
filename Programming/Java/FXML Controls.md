# Introduction to FXML Controls.
There are a wide range of FXML Controls with in the **javafx.scene.control** package. The more simplistic ones such as `buttons` and `text fields` are usually self-explanatory, but there are more complex components that require greater focus and attention to detail when it comes to all the nuances and behaviors they provide.

# Tree Table View
A Table View is a JavaFX widget  that is a rather complex *"black box"* widget made up of buttons, text fields, labels, and an internal layout. It provides a spreadsheet-like presentation of a set of data, with rows and sortable columns.

Rows in the table represent various data attributes of objects in your program. They can be select and the selection can be determined in our code. Objects are maintained in an [ObservableArrayList](JavaFX,%20It%20Has%20The%20Beans!.md#ObservableArrayList), and additions / modifications / removals to that list, will be represented in the display.