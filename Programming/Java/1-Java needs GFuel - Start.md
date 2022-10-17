***Disclaimer***
This Document contains some of the nuances of working with JavaFX using the [MVC](../MVC.md) design methodology.


# Getting Started
There are several Documents that contribute to understand the ~~disaster~~... ~~cluster fuck~~ .... ~~God awful headache.~~.. Okay I'm sure someone somewhere must like Java... and if you are one of those people then you can go ~~*Fuck Yourself*~~ enjoy the experience.

# Index and References
- Noob's - Review here first:
[An Introduction to Programming](An%20Introduction%20to%20Programming.md)
[Data Types](Data%20Types.md)
[MVC](MVC.md)
 - Intermediate Noob's start here:
[JavaFX, It Has The Beans!](JavaFX,%20It%20Has%20The%20Beans!.md)
[FXML Controls](FXML%20Controls.md)
[Scene Builder](Scene%20Builder.md)

# Classes
Java seems to have stolen Linux's _"Everything is a file!"_ concept and thought, _"Hey! Lets call everything a class even when its just a single function or global declaration"_ cause that _TOTALLY_ makes sense. The idea I'm guessing is to force the programmer to adopt [OOP](An%20Introduction%20to%20Programming.md) whether they want to or not!

## Parent Class
Also called a `Super Class`, it is a class from which any sub or child classes are derived. As an example if you wanted to have a cat class you would derive that from an Animal class. [Refer to this code example](1-Java%20needs%20GFuel%20-%20Start.md#^cc12f4).
 
## Child Class
Also called a `Sub Class`, is used to apply [Polymorphism](An%20Introduction%20to%20Programming.md###Polymorphism) to the [parent class](1-Java%20needs%20GFuel%20-%20Start.md##Parent%20Class). The example below demonstrates a child class **Cat** extending the Parent Class **Animal**. Here the parameters from the parent class are called with the public definition of Cat and then inside that class we defined new methods and values such as what sound a cat makes and how to define it.

 ```java
 //File: Cat.java
 
 public class Cat extends Animal {
	
	private String sound;
 
 	public Cat(String breed, String behavior, int lifespan, double price) {
		super(breed, behavior, lifespan, price); //parameters from the super class Animal
	}
	
	public String getSound() {
		return "Meow!";
	}
	
	public void setSound(String sound) {
		this.sound = sound;
	}
	
 }
 ```

^ac3236

Note: The use of the [`super` keyword](1-Java%20needs%20GFuel%20-%20Start.md##The%20`super`%20keyword) on the first line within the creation of the Cat Class is a special keyword to reference the parameters defined in the Parent Class.


# Access Modifiers
There are three, though technically four options. for defining scope of a method or variable within a class.

|   Modifier  | Class | Package | Subclass | World |
|:-----------:|:-----:|:-------:|:--------:|:-----:|
|public      |   Y   |    Y    |     Y    |   Y   |
|protected   |   Y   |    Y    |     Y    |   N   |
|default or no modifier |   Y   |    Y    |     N    |   N   |
|private     |   Y   |    N    |     N    |   N   |

## Public
The `public` keyword is an access modifier used for classes, attributes, methods and constructors, making them accessible by any other class.
## Protected
The `protected` keyword is an access modifier used for attributes, methods and constructors, making them accessible in the same package and subclasses.
## No Modifier or default
No specified modifier is similar to protected however the class is only accessible to other classes within the package.
## Private
The `private` keyword is an access modifier used for attributes, methods and constructors, making them only accessible within the class it has been declared in. This is a common practice to ensure [encapsulation](An%20Introduction%20to%20Programming.md#Encapsulation). If it is applied to an object it is modified using [public](#Public) `setter` and `getter` functions.

# Non-Access Modifiers
Non-Access modifiers are special keywords that help define access to class members

## Static 
In Java there is no such thing as Global Variables or Methods. Everything has to sit inside of a class one way or another. So the way around it this obstacle is to define a method or variable as `static`.  **Static Class Method** and **Static Class Variables.**
### Static Class Method:
A class method that can be called without instantiating the class.
### Static Class Variable
A class variable that can be accessed without instantiating the class.

## Abstract
A class modified with **abstract** cannot be [instantiated](An%20Introduction%20to%20Programming.md#Instantiation), unlike Concrete Classes. It is used to create a parent class that wont be called. It will only be used to derive sub-classes. The use of the abstract keyword defines the class as abstract. [This example demonstrates the syntax to make a class abstract](1-Java%20needs%20GFuel%20-%20Start.md#^cc12f4).

# Casting
Is used for converting one Data Type to another. This can be done on both [Primitive and Non-Primitive Data Types](Data%20Types.md#Primitive%20and%20Non-Primitive%20Data%20Types). For an example with a `Non-Primitive Data Type`, using the [Animal](1-Java%20needs%20GFuel%20-%20Start.md#^cc12f4) and [Cat](1-Java%20needs%20GFuel%20-%20Start.md#^ac3236) code references. We create a Cat Object from the superclass animal.

```java
Animal Cat1 = new Cat ("Ragdoll", "Calm" , 19 , 1500.00);
```
Animal is the superclass type and Cat1 is the object name. We are defining the new object as a subclass Cat. Since your starting point is the superclass you can call the superclass methods such as:

```java
System.out.println(Cat1.getBreed);
System.out.pringln(Cat1.getBehavior);
```
*However....* you won't be able to call the subclass members such as getSound(), because the subclass object Cat1 is a new Animal superclass type and the Animal class doesn't have a getSound() method. 

So to allow a superclass type object to utilize its subclass methods you have to cast the method into the object like this:

```java
System.out.println (((Cat)Cat1).getsound());
```
The `(Cat)` referees to the subclass name that `Cat1` ( the name of the new Animal Type Object) is being `Cast` into, and ``.getsound()`` is the method from the Cat Class we want to utilize.

Another place casting is utilized is in [Event Handling](JavaFX,%20It%20Has%20The%20Beans!.md#Event%20Handler) to determine the Datatype of an Event Object when an event is triggered, enabling Event Object members to be accessed in the handler.

# Overloading
Just like in C++ you can create methods or constructors with the same name but different signatures by using different parameters. Access modifiers, return types, and specifiers can be different when Overloading a method. Ideally you could use this if you wanted to instantiate an object that has one **OR** possibly additional parameters. 

The same method name with different parameters could have different functions and would only be called when the use of that method included the second parameter vs just a single parameter. In VBA you don't overload, you simply define `optional` parameters and case statements within the classes to handle them.

# Overriding
In any object-oriented programming language, Overriding is a feature that allows a subclass or child class to provide a specific implementation of a method that is already provided by one of its super-classes or parent classes. When a method in a subclass has the same name, same parameters or signature, and same return type(or sub-type) as a method in its super-class, then the method in the subclass is said to _override_ the method in the super-class.

![Method Overriding example](/Images/overriding-in-java.png)

Method overriding is one of the way by which java achieve [Run Time Polymorphism](https://www.geeksforgeeks.org/dynamic-method-dispatch-runtime-polymorphism-java/).The version of a method that is executed will be determined by the object that is used to invoke it.

**NOTE:** #GoodCodingPractice
When overriding a method, it is good practice to define `@Override` above the method being overridden as seen in this [example](JavaFX,%20It%20Has%20The%20Beans!.md#^38cadb). It is not required but doing so will ensure that at compile time if the method being overridden is misspelled or unavailable the program will flag it because it can't find the method to which is being requested to be overridden. ^6c58e6

# Special Operators in Java
## The `this` keyword
Th `this` keyword can be used to reference an instance of a Class and can be used to access instance members. It saves time in not having to come up with a different instance variable to define a parameter variable. 
*NOTE:* `this` can not be applied to a class variable or static members.

The example below demonstrates the use of `this`

```java
//File: Animal.java

//Declared Fileds
private String breed, behavior;
private int lifespan;
private double price;

//Public Class definition called animal
public abstract class Animal(String breed, String behavior, int lifespan, double price) {
	this.breed = breed;
	this.behavior = behavior;
	this.lifespan = lifespan;
	this.price = price;
}

//getBreed Method
public String getBreed() {
	return breed;
}

//setBreed Method
public void setBreed(String breed) {
	this.breed=breed;
}
//getBehvaior Method
public String getBehavior () {
	return behavior
}
```

^cc12f4

## The `super` keyword

The `super` keyword which is not the same thing as `super()` allows access to `protected` and `public` members of a [superclass](1-Java%20needs%20GFuel%20-%20Start.md##Parent%20Class). In this example the `super.setbreed` action is calling the `setbreed` method from animal and then overriding the use of that method within the Dog subclass. This essentially provides the same function as [method overriding](1-Java%20needs%20GFuel%20-%20Start.md#overriding). It is a better way to do this cause... reasons?

```java
//File: Dog.java

public class Dog extends Animal {

	private String sound;
	
		public class Dog (String breed, String behabior, init lifespan, double price) {
			super(breed, behavior, lifespan, price) //identifies the parameters in the parent class
			super.setbreed("Bulldog"); //Overriding the setbreed method when a dog object is instantiated
		}

	public String getSound() {
		return "Woof!";
	}
	
	public void setSound(String sound) {
		this.sound = sound;
	}
}
```
## instanceof keyword

Instanceof allows us to ask an object if it is a member of a specific class such as:

```java
if(dog instanceof Dog){
	system.out.println("dog is an instance of Dog")
} else {
	system.out.println("this object is not an instance of Dog")
}
```
# Special Classes
## Wrapper Classes
Every Java [primitive data type](Data%20Types.md#Primitive%20Data%20Types) has a corresponding class defined in the Java API class library.

This class is sometimes called a wrapper class because it wraps a primitive value with the object-oriented equivalent to make the primitive type look and behave like an object.

The following table lists the wrapper classes for each of the eight primitive types.


| Primitive Type 	| Wrapper Class 	|
|----------------	|---------------	|
| int            	| Integer       	|
| short          	| Short         	|
| long           	| Long          	|
| byte           	| Byte          	|
| float          	| Float         	|
| double         	| Double        	|
| char           	| Character     	|
| boolean        	| Boolean       	|

## Special Methods

## Special Objects



# Tips for Troubleshooting errors with IntelliJ
The error window at the bottom is called a **stack trace**. In there it yields hints as to what could be the problem.

1. It says "**No controller specified**". This means the controller is not set in the fxml and it doesn't know where to go
2. It says "**Error resoloving onAction="#someEventHandler"**". This means the event handler is not in the controller code.
3. It says "**Location is Required**"- the path you gave the FXMLLoader.load() command for the file is most likely not correct.
	- The src directory should be the leading slash, like "/view/myscreen.fxml" means the file is in src/view/myscreen.fxml. The path is case sensitive.
4. You have a "**NullPointerException**" at some line in the very bottom of the report - This means there is an object on that line you have not initialized.