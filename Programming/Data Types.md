# Introduction to Language Types
There are essentially two functional types of languages, **Statically Typed Language** vs **Dynamically Typed Languages**.  Additionally, there are two types of strength in languages, **Strongly-Typed** vs **Weakly-Typed**. Resulting in 4 unique types in total.

This document details the differences as well as the different languages that use either Dynamic or Static  and Strongly-Typed or Weakly-Typed and how they may differ from other languages of the same type.

 ## Statically Typed Languages
 These are languages where each variable and expression type is already known at compile time. Once a variable is declared to be of a certain data type, it cannot hold values of other data types. 

 ## Dynamically Typed Languages
 These where variables and expression types may not be known at compile time and those languages can receive different data types at runtime. 

## Strongly-Typed Languages
 A strongly-typed language is where the language is explicit and does not allow implicit conversions between unrelated data types. As an example if you had a string variable containing numbers, the language would not automatically convert that string into a int, or double, etc.
 
## Weakly-Typed Languages
 A weakly-typed language is the exact opposite of a [Strongly-Typed Languages](#Strongly-Typed%20Languages). It will preform implicit conversions between data types. So if you were to hold a number inside a string it would automatically convert that value and treat its contents as a number rather than a string value.

# The Four Unique Language Types
![](Language-Type-Matrix.png)
## [Statically Typed](#Statically%20Typed%20Languages) and [Strongly-Typed Languages](#Strongly-Typed%20Languages)
- C#
- JAVA
- F#
- Haskell
## [Statically Typed](#Statically%20Typed%20Languages) and [Weakly-Typed Languages](#Weakly-Typed%20Languages)
- C
- C++ 
## [Dynamically Typed](#Dynamically%20Typed%20Languages) and [Strongly-Typed Languages](#Strongly-Typed%20Languages)
- Python
- Ruby
- Clojure
## [Dynamically Typed](#Dynamically%20Typed%20Languages) and [Weakly-Typed Languages](#Weakly-Typed%20Languages)
-  Perl
- JavaScript.
- VB
	- Although VB fits into this category, you can make it a behave as a  [Statically  Strongly-Typed Language](#Statically%20Typed%20Statically%2020Typed%2020Languages%20and%20Strongly-Typed%20Languages%20Strongly-Typed%2020Languages) by using the [`Option Explicit`](https://docs.microsoft.com/en-us/office/vba/language/reference/user-interface-help/option-explicit-statement)statement before any subroutines or functions within a module.


# Primitive and Non-Primitive Data Types
## Primitive Data Types
Primitivize Data Types are only single values and have no special capabilities. In most languages there are only 8 types. 
1. **Boolean**: boolean data type represents only one bit of information **either true or false**. By default in most languages a declared boolean is **false** until made **true**
2. **Byte**: the byte data type is an 8-bit signed two’s complement integer. The byte data type is useful for saving memory in large arrays. The value can be between -128 and 127
3. **Char**: The char data type (Character) is a single byte or 8-bit ASCII character ^45a375
> ### Java
> Unlike in C/C++ a Java uses the Unicode system not ASCII. Because Unicode is so much larger 8bits is not enough to represent all the characters so a char is 2 bytes or 16-bits 
4. **Short**: The short data type is a 2 byte or 16-bit signed two’s complement integer. Similar to byte, use a short to save memory in large arrays, in situations where the memory savings actually matters.
5. **Int**:  An integer data type is a 4 byte or 32-bit signed two’s complement integer.
6. **Long**: The long data type is a 8 byte or 64-bit two’s complement integer.
7. **Floating Point**: The float data type is a single-precision 4 byte or 32-bit [IEEE 754](https://en.wikipedia.org/wiki/IEEE_floating_point) floating-point. Use a float (instead of double) if you need to save memory in large arrays of floating-point numbers. It can hold up to 7 decimal digits.
8. **Double**: The double data type is a double-precision 8 byte or 64-bit IEEE 754 floating-point. For decimal values, this data type is generally the default choice because it can hold up to 16 decimal digits.

## Non-Primitive Data Types OR Reference Data Types
Basically anything that doesn't fit into a [Primitive Data Type](#Primitive%20Data%20Types) is considered a Non-Primitive. The most common among almost all languages are 
1.  **String**: Strings are defined as an array of [characters](#^45a375). 
> ### Java Strings
> The difference between a character array and a string in Java is, the string is designed to hold a sequence of characters in a single variable whereas, a character array is a collection of separate char type entities.
2. **Array**: An array is a group of like-typed variables that are referred to by a common name.  Some languages implement Arrays differently. See the subheading below for more detail 
> ### Java Arrays
> Arrays in Java work differently than they do in C/C++. The following are some important points about Java arrays.
> -   In Java, all arrays are dynamically allocated. (discussed below)
> -   Since arrays are objects in Java, we can find their length using member length. This is different from C/C++ where we find length using size.
> -   A Java array variable can also be declared like other variables with [] after the data type.
> -   The variables in the array are ordered and each has an index beginning from 0.
> -   Java array can be also be used as a static field, a local variable or a method parameter.
> -   The **size** of an array must be specified by an int value and not long or short.
> -   The direct superclass of an array type is [Object](https://www.geeksforgeeks.org/object-class-in-java/).
3. **Object**: It is a basic unit of Object-Oriented Programming and represents the real-life entities.  An object consists of : 
	 1.  **State**: It is represented by attributes of an object. It also reflects the properties of an object.
	 2.  **Behavior**: It is represented by methods of an object. It also reflects the response of an object with other objects.
	 3.  **Identity**: It gives a unique name to an object and enables one object to interact with other objects.
4. **Class**: A class is a user-defined blueprint or prototype from which objects are created.  It represents the set of properties or methods that are common to all objects of one type. In general, class declarations can include these components, in order:
	1.  **Modifiers**: A class can be public or has default access (Refer [this](https://www.geeksforgeeks.org/access-specifiers-for-classes-or-interfaces-in-java/) for details).
	2.  **Class name:** The name should begin with a initial letter (capitalized by convention).
	3.  **Superclass(if any):** The name of the class’s parent (superclass), if any, preceded by the keyword extends. A class can only extend (subclass) one parent.
	4.  **Interfaces(if any):** A comma-separated list of interfaces implemented by the class, if any, preceded by the keyword implements. A class can implement more than one interface.
	5.  **Body:** The place where the go juice goes. In many languages, the class body  is surrounded by braces, { }.
5. **Interface**: Like a class, an interface can have methods and variables, but the methods declared in an interface are by default abstract (only method signature, nobody). 
	 -   Interfaces specify what a class must do and not how. It is the blueprint of the class.
	 -   An Interface is about capabilities like a Player may be an interface and any class implementing Player must be able to (or must implement) move(). So it specifies a set of methods that the class has to implement.
	 -   If a class implements an interface and does not provide method bodies for all functions specified in the interface, then the class must be declared abstract.