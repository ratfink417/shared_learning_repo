# Model-View-Controller
 The **Model-View-Controller (MVC)** is an architectural pattern that separates an application into three main logical components: the **model**, the **view**, and the **controller**. Each of these components are built to handle specific development aspects of an application. MVC is one of the most frequently used industry-standard web development framework to create scalable and extensible projects.
 
![alt ><](MVC_model_view_controller.jpg)
 
 ### Model

The Model component is essentially the meat and potatoes. This can be either the data that is being transferred between the View and Controller components or any other business logic-related data such as retrieving data from a remote database. For example, a Customer object will retrieve the customer information from the database, manipulate it and update it, send data back to the database, or use it to render data. All of this is handled within the Model

### View

The View component is used for all the UI logic of the application. Its rather self explanatory, but any GUI related thing fall within this component.

### Controller

The Controllers is just like an air traffic controller it controls the interface between Model and View components to process all the business logic and incoming requests, manipulate data using the Model component and interact with the Views to render the final output. For example, the Customer controller will handle all the interactions and inputs from the Customer View and update the database using the Customer Model. The same controller will be used to view the Customer data on the Customer Data screen