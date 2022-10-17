# Functions
Just an example that covers most of the basics with python functions and how to write one.

```python
def hello_func(greeting, name='You'):
	return '{}, {}'.format(greeting, name)
	
print(hello_func('Hi'))
print(hello_func('wassup', 'derp'))
```
- This example defines the function _hello\_func_ which accepts 2 arguments _greeting_ and _name_. 
- The argument _name_ will assume the default string value of **'You'** if you don't assign it a value when you call it.
- After the function runs, it returns a formatted string that makes use of the two arguments you give the function when you call it.

**OUTPUT**
```output
Hi, You
wassup, derp
```
As you can see when no value is given to the second argument _name_, the value **'You'** is taken implicitly and when you give values to both arguments, they are assigned positionally and used in the return value.

# Passing an undefined list of arguments
Sometimes you will want to have a function that can take any number of arguments to work with. By convention when you see this being used, it will be a function definition that looks like this.

```python
def my_func(*args, **kwargs):
	print(args)
	print(kwargs)
	
my_func('foo', 'bar', 'baz', name='Derp-Man', age=-42)
```

This will print the arguments like this

```output
('foo', 'bar', 'baz')
{'name': 'Derp-Man', 'age': -42}
```
The extra parens and curly's are there to give you info about the type for the stuff being printed. In this case _args_ being **('foo', 'bar', 'baz')** means that it's a 3-tuple and _kwargs_ printing like **{'name': 'Derp-Man', 'age': -42}** means that it is a dictionary of key-value pairs.

## But why is those things?
The _unpacking operators_ \* and \*\* in Python tell the interpreter to _unpack_ the variable to the right of the \* or \*\* as an [iterable](Python%20Iterartors%20and%20Generators.md) or a dictionary respectively. 

When unpacking an iterable, the operator takes each item contained in the object to the right of \* and returns them **individually** (as atoms). For example look at the difference here.

```python
my_list = [1, 2, 3]

# print the list as a variable
print(my_list)

# print each atom in the list
print(*my_list)
```
- The first print statement gives you `[1, 2, 3]` square brackets indicating that you are looking at a _list_
- The second print statement gives you `1 2 3` three individual values. as if you called `print(1, 2, 3)`

You could even see this behavior using [[Python Introspection]] tools. Or by just observing when you unpack a list of three into a function that accepts less than 3 arguments.

# What Does Python Think Functions Are
Python treats functions as _First-Class Citizens_. This means that they:
- Can be passed as arguments to functions
- Can be returned by functions
- Can be assigned to a variable
- They have a type ie _will print something when you use print(typeof(some\_fucntion))_
- Generally support all other operations available to other entities in the language (objects,vars,classes...)

The () symbols are operators used by functions to indicate that the function to the left of ( is _called_ by the interpreter with the arguments between the () symbols

The example code below shows what all of this looks like when you apply it's meaning to python scripting.

```python
def square(x):
	return x * x

def my_map(my_func, arg_list):
	result = []
	for i in arg_list:
		result.append(my_func(i))
	return result

squared_nums = my_map(square, [1,2,3,4,5])
print(squared_nums)
```
```output
[1,4,9,16,25]
```
The script shows a function being used as a _first-class citizen_ in the language. After it's definition, the script goes on to:
- use the function as an argument to another function as seen in the line that calls the _my\_map_ function `squared_nums = my_map(square,[1,2,3,4,5])`
- It gets assigned to a variable when that same line enters the _my\_map_ function. It's assigned to the variable _my\_func_
- It gets called again as a function with the _()_ operator in a loop against each value in _arg\_list_ 

Don't forget that it's not that you can just pass function into a function as an argument but you can also return functions from another function in this paradigm.

```python
def logger(msg):
	def print_log_message():
		print('LOG: ',msg)
	return log_message

log_hi = logger('Hi')
log_hi()
```
```output
LOG: Hi
```
So, in this example you:
- pass a message _msg_ into the function _logger_
- _logger_ defines a function that **it** calls _print\_log\_message_ which just prints "LOG: " followed by whatever you put in the _msg_ argument.
- _logger_ returns the function _print\_log\_message_ which you could use just like we did earlier when we defined _square_.
- we assign the returned function to a variable name _log\_hi_ by calling _logger('HI')_ which sort of constructs a function for use given that message value.
- when we call _log\_hi()_ we call a function that prints 'LOG: Hi' that was defined in the _logger_ function given the argument **'Hi'**

These returned inner functions created by a sort of outer function using it's given parameters, scope and variables is sometimes called a _Closure_

# Decorators
Decorators use the same technology that Closures use ie(you can pass and return functions from other functions) to add some top level functionality another function. For example if you had some sorting functions that you wanted to get the execution time from, you might create a decorator that accepts a sorting function and notes the start/end time and returns or prints the diff.

A simple example of a decorator might look like this
```python
def decorator_function(original_function):
	def warpper_function():
		return original_function()
	return wrapper_function

def display():
	print('display function ran')

decorated_display = decorator_function(display)

decorated_display()
```
```output
display function ran
```
What happens here is:
- _decorated\_display_ is assigned the **return value** of the call `decorator_function(display)`
- that call is passing the _display_ function - which just prints our message - to the _decorator\_function_
- this function creates it's own internal function called _wrapper\_function_ which doesn't do anything but return the argument that was passed to it (_display_) in our example
- now the wrapped function (you could have added any other code to _wrapper\_function_ that you wanted) is returned
- finally, we call _decorated\_display()_ which runs our modified **wrapped** version of the _display_ function. 

While this does save us some time in implementing functions that might just vary a little bit from the original one we are trying to run, it is admittedly a lot of syntax. So Python includes the decorator syntax which makes this easier and more memorable and it looks like this example of a decoration that gives you the execution time of the decorated function.

```python
def my_logger(orig_func):
	import logging
	logging.basicConfig(filename='{}.log'.format(orig_func.__name__), level = logging.INFO)
	
	def wrapper(*args, **kwargs):
		logging.info('Ran with args: {}, and kwargs {}'.format(args,kwargs))
		
@my_logger
def display_info(name, age):
	print('display_info ran with arguments ({}, {})'.format(name, age))

display_info('Hank', 30)
```
```output
display_info ran with arguments (Hank, 30)
```

Notice now, that we don't have to remember to take the defined function and create a new sort of 'instance' of that function which gets equated on the left side of the decorator an equal sign to the wrapping function. Instead, with _decorator syntax_ all that is needed is the _@decorator-name_ above the definition of the new function and python automatically knows what we intended. Actually if you had multiple decorators declared in your script you could stack the decorators on top of each other and have multiple wraps around the function.




