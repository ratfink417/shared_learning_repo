The two most widely known types of programming design are [Functional Programming](An%20Introduction%20to%20Programming.md#An%20Introduction%20to%20Functional%20Programming) and [Object-Oriented Programming](An%20Introduction%20to%20Programming.md#An%20Introduction%20to%20Object-Oriented%20Programming) AKA **OOP**.

# An Introduction to Functional Programming 

#PLACE-HOLDER-FOR-CONTENT 


# An Introduction to Object-Oriented Programming
OOP is a programming paradigm in which you program **_using objects to represent things_** you are programming about (sometimes real world things). It centers around separating the parts of a program down two main classifications, *Classes* and *Objects*

### Classes
A class is essentially a definition or blueprints of an [object](An%20Introduction%20to%20Programming.md###Objects) . Just like a blueprint is the design of a house before it is built. It describes the [attributes](An%20Introduction%20to%20Programming.md###Attributes) and [behaviors](An%20Introduction%20to%20Programming.md###Methods) of an object, but it is not the object itself. A better example can be a specific brand of car is Honda and your car is a 2008 Honda Civic EX Coupe. The class is Honda which has a property called models. It can be Civic, Accord, CRV, etc. They have a property called body which is Coupe, Sedan, or hatchback. The Class defines all of this information. It also defines behavior which are actions that can be performed with the class. Using the same example a Honda Vehicle can be driven forward, driven backwards, it can turn left, it can turn right, it can brake, etc.

### Objects 
Exactly as it sounds. Everything on the screen is an object. Windows, buttons, text, etc. Anything and everything is an object. They are defined by a [class](An%20Introduction%20to%20Programming.md###Classes). Using the Honda example for Class, your car object is a Honda Civic 2008 EX Coupe. Civic, 2008, EX, and coupe are all defined properties within the Honda Class. The fact you can drive it, park it, turn it, etc. are behaviors called [Methods](An%20Introduction%20to%20Programming.md###Methods) that you can do with your Honda object which is also defined in the class. You [instantiate](An%20Introduction%20to%20Programming.md#Instantiation) the Coupe class which [inherits](An%20Introduction%20to%20Programming.md#Inheritance) the [attributes](An%20Introduction%20to%20Programming.md###Attributes) and [behaviors](An%20Introduction%20to%20Programming.md###Methods) of the parent classes above it. Honda, Civic, etc. producing your specific object a 2008 Honda Civic EX Coupe.

## A-PIE Paradigm
In Object Oriented Programming there are 4 main concepts that the paradigm is built upon. Abstraction, Polymorphism, Inheritance, and Encapsulation. An easy acronym to remember this would be ***A PIE***.

### Abstraction 
^88222b

Focuses on the essential qualities of a [Class](An%20Introduction%20to%20Programming.md###Classes). A table for example can be many things; large or small, wooden or metal, 4 legged or single column, etc. but the idea of a table is the same. It focuses on the essentials. A table is an elevated platform, it has height, width, and length. These are essentials necessary to make a table a table. The details of its design such as color, shape, material are irrelevant. In Abstraction the irrelevant and unimportant are ignored. It's a concept about generalization. Referencing the Honda Civic mentioned in the class example. Abstraction would define that it is a Car, with four wheels, and can be driven along the ground.


### Encapsulation
Encapsulation is about segregation and isolation. Taking [attributes](An%20Introduction%20to%20Programming.md###Attributes) and [behaviors](An%20Introduction%20to%20Programming.md###Methods) and bundling them together in the same [class](An%20Introduction%20to%20Programming.md###Classes). One reason being obvious because of their likeness and purpose, but also for restricting access to any [objects](An%20Introduction%20to%20Programming.md###Objects) based on that class. Sometimes referred to as *data hiding* or *black boxing*, it is based on the idea that an object should not provide access to anything about itself other than what is necessary for it to work within the application. The main purpose for this isn't specifically about security but mainly for the need of changes and upgrades. If you limit how an application accesses information from an object down to a [behavior](An%20Introduction%20to%20Programming.md###Methods)  when the way that object works changes the only update you need to apply is how that behavior provides the application with what it needs. 

Using the car as an example an engine would be the program or application. It has many objects that work together in order for it to run. We need vaporized fuel for engine combustion. There are two classes of objects that can do this, a carburetor or a fuel injection system. The function or method if we speak programmatically is the same, but the *way* they work is very different. Both the carburetor object and the fuel injection system object are encapsulated. If you convert a car from a carburetor to fuel injection the engine still gets its vaporized fuel. How that fuel became vaporized is all handled in the [methods](An%20Introduction%20to%20Programming.md###Methods) of the object. Encapsulation allows us to convert the vehicle engine from one object to another without redesigning the whole engine.

### Inheritance
Is essentially a time savings technique. The idea behind inheritance is when creating classes that are similar we can copy or INHERIT [attributes](An%20Introduction%20to%20Programming.md###Attributes) and [behaviors](An%20Introduction%20to%20Programming.md###Methods) from another [class](An%20Introduction%20to%20Programming.md###Classes). The class you inherit from is referred to as the **SuperClass** / **Parent Class**. The class inherited ***to*** is referred to as the **SubClass** / **Child Class**. This serves two purposes. One to make class creation with similar behaviors and attributes quicker and easier. The other reason is to incorporate [Polymorphism](An%20Introduction%20to%20Programming.md###Polymorphism). An example taken from the class definition mentioned earlier is Honda makes cars. Since they have a variety of models with different properties each model would be a different class. Civic, Accord, CRV, Pilot are all models. Each model however has different packages and builds. Using Civic as an example, instead of creating a new class for  Sedan or coupe from scratch. You would create a class for sedan or coupe that would inherit from the Civic Class. It would contain all the properties that makes a civic a civic in addition to what makes a civic coupe or a civic sedan. The beautiful thing about this, is if you change the design of the Civic class such as a different engine, that new engine is immediately updated in the civic and sedan classes because the engine is an inherited attribute from Civic.

### Polymorphism

Goes hand and hand with [Inheritance](An%20Introduction%20to%20Programming.md###Inheritance). It literally means *many forms*. It essentially refers to one [function](An%20Introduction%20to%20Programming.md###Function) or [method](An%20Introduction%20to%20Programming.md###Methods) meaning the same thing but working a different way as needed. An example maybe the method in which a vehicle tracks fuel economy. The sedan or coupe civic would all use the fuel economy method from the inherited Civic [class](An%20Introduction%20to%20Programming.md###Classes); however, what about a vehicle with alternative fuel options such as the civic hybrid? That vehicle would use the same fuel economy method for calculating fuel consumption; however, that method would be altered or overridden to account for the distance covered using the battery system. If inheritance is about what makes classes similar, polymorphism is about what makes those similar classes unique.

## Program Components in OOP

### Events
Events are exactly as they sound. Triggers such as opening a window, or pressing a key.  One of the most typical and widely used events is the “click”. Here is an example in VBA:
``` VBA
Sub Object_Click ()  
  
 ' Do something in reaction to this click  
 DoSomething  
  
End Sub
```
Whenever which ever object gets clicked, VB will run this code as a reaction to the click.

### Attributes
Synonymous with the word Properties, attributes describe an object in detail such as shape, location, color, etc.

### Methods
Synonymous with the word Behavior, the term method is used almost exclusively in object-oriented programming languages. A method is a built-in [subroutine](An%20Introduction%20to%20Programming.md###Subroutines) or [function](An%20Introduction%20to%20Programming.md###Function) but limited to part of a class.

### Subroutines
A sequence of program instructions that perform a specific task, packaged as a single unit.

### Function
The same as a [subroutine](An%20Introduction%20to%20Programming.md###Subroutines); however, a function can return and/or receives values. Some programming languages do not have subroutines at all such as [Java](JavaFX,%20It%20Has%20The%20Beans!.md) and C++. Instead they use functions with `void` following the function name indicating the function returns no value making it behave like a subroutine.

# Common Terms Found in OOP

## Instantiation
Often referred to with it's transient verb **instantiating**.  The phrase "instantiating a class" means **the same thing as "creating an object"**; you can think of the two as being synonymous. When you create an object, you are creating an instance of a class, therefore "instantiating" a class.


