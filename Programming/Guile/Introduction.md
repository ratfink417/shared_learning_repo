# GNU Guile
The Guile programming language is an implementation of the "scheme" standard in the same way that UNIX or Linux are implementations of the POSIX standard. It was created by the GNU project as an extension language for GNU software because scheme's minimal design makes it easy to implement the domain specific language that Guile aims to be with the flexibility and ease of use baked into the scheme standard.

# Polish Notation
One of the most visibly Lisp things you'll notice about scheme or most Lisps for that matter is the parsing order. I mean the way you create a statement in most Lisps follows this sort of deliniation.`(thing-to-do parameter1 parameter2 ...)`. Like look at a couple of these examples.
```scheme
;; Returns 4
(+ 1 3)

;; Returns "Hello World"
(string-append "Hello " "World")

;; Returns "abcdefg" also shows off a little mor of what this looks like in 
;; practice 'apply' is given two arguments 'string-append and that list 
;; returns some value from the expression in it's parentheses which maps  
;; symbol to a list of strings string-append in the end ends up operating  
;; on 7 strings "a" "b" "c" ... 
(apply string-append (map symbol->string '(a b c d e f g)))

;; If you don't appreciate the simplicity of this yet, take a look at this 
;; factorial using recursion function in scheme
(define (factorial x)
  (if (= x 0)
	  1
	  (* x (factorial (- x 1)))))

;; If you aren't beguiled by this guile function, you might not really be 
;; into Lisp

```
## Guile/Scheme is "Latently Typed"
 _Latent Typing_ describes a method for categorizing data in which you cannot, _in general_ just determine what kind of data will be associated with a particular variable, or with the result of an expression. So.... you don't just give your data a type like in C/C++, Scheme figures that all out during run time.

I mean... of course, you _can_ tell from the code what the type of an expression will be in simple cases like if you have a line in your program that sets the variable `x` to the numeric value 1 _(and it's a single threaded program)_  then, you can be pretty sure that `x`  will have a numeric value after this line runs. 

The point is, that Scheme doesn't have anything on it's own which requires the procedure parameters to always have a type and there is no way of declaring this sort of constraint in your program.   So you won't be declaring variables with types, constraining parameters  of a procedure to a specific type of value or return types from your procedures. 

The literal values themselves have types NOT the variables.  If you _need_  to check that a value has the expected type, there are run time procedures that do this. 

# Defining Variables
Simple declarations  are done with the `define` keyword and the value of a named variable is changed with the `set!` keyword.

```scheme
;; Make a variable `x' with initial numeric value 1.
(define x 1)

;; Make a variable `organization' with an initial string value.
(define organization "Free Software Foundation")

;; Change the value of `x' to 5.
(set! x 5)

;; Change the value of `organization' to the FSF's street number.
(set! organization 545)

;; Change the value of 'organization' to the output of an expression
(set! oranization (+ x 9))
```
# Procedures or Functions
In scheme procedures are treated just the same as any other value. You might have noticed in some of the examples earlier that nothing special needs to happen for you to give a function a variable name or to use it as a parameter in another procedure. In fact, returning a procedure isn't even different from returning any other kind of data. That's one of the cool things about this, code is just data in scheme.

One of the neat consequences of this is that you can effectively copy a procedure or change the name of a procedure just as simply as you could do it with a variable value.

```scheme
;; The original name for this function is too long so create a new 
;; procedure with the same value that you could call instead. Now
;; both names work
(define call/cc call-with-current-continuation)

;; Get rid of the run-time value of the original function name so
;; that it can't be used anymore
(set! call-with-current-continucation nil)
```
## Invoking Procedures
A procedure invocation in Scheme is written like this:
```scheme
(procedure [arg1 [arg2 …]])
```
For example, `string-append` is a standard Scheme procedure that concatenates all the arguments, which are expected to be strings, that it is given. So the expression
```scheme
(string-append "/home" "/" "andrew")
```
will return the string value `"/home/andrew"`.

Similarly, `string-length` is a standard Scheme procedure that returns the length of a single string argument, so `(string-length "abc")` will return  the numeric value 3.

Each of the parameters in a procedure invocation can itself be any Scheme expression. Since a procedure invocation is itself a type of expression, we can put these two examples together. 
```scheme
(string-length (string-append "/home" "/" "andrew"))
```
returns the numeric value 12.

You may be wondering what happens if the two examples are combined the other way round. If we do this, we can make a procedure invocation expression that is _syntactically_ correct:
```scheme
(string-append "/home" (string-length "abc"))
```
but when this expression is executed, it will cause an error, because the result of 
`(string-length "abc")` is a numeric value, and `string-append` is not designed to accept a numeric value as one of its arguments _semantically_, this is a problem

There does exist a procedure for turning integers into strings that you could use to get this to work but the thing to keep in mind is that this isn't a type casting operation, it's the evaluation of a procedure.

```scheme
(string-append "/home" (number->string (string-length "abc"))
```
## Creating Procedures
Scheme has lots of standard procedures which Guile provides via predefined top level variables. Eventually, you'll wanna create your own procedures that encapsulate aspects of your own applications’ functionality. To do this, you can use `lambda` syntax which looks like this
`(lambda (name address) body …)`

This will create a new procedure that takes two arguments: `name` and `address`. 

The new procedure is determined by the sequence of expressions and definitions in the _body_ of the procedure definition. 

Typically, body would use the arguments in some way, or else there wouldn’t be any point in giving them to the procedure. When invoked, the new procedure returns the value of last expression in the body.

To make things more concrete, let’s suppose that the two arguments are both strings, and that the purpose of this procedure is to form a combined string that includes these arguments. Then the full lambda expression might look like this:
```scheme
(lambda (name address)
  (string-append "Name=" name ":Address=" address))
```

 Because the procedure part of an invocation expression can be any Scheme expression whose that returns a procedure and that’s exactly what a lambda expression is. So we can use a lambda expression directly in a procedure invocation, like this:
```scheme
((lambda (name address)
   (string-append "Name=" name ":Address=" address))
 "FSF"
 "Cambridge") 
```
This is a valid procedure invocation expression, and its result is the string:
"Name=FSF:Address=Cambridge"

It is more common, though, to store the procedure value in a variable ie: 
```scheme
(define make-combined-string
  (lambda (name address)
    (string-append "Name=" name ":Address=" address)))
```
Feels a lot like defining a function in any other language right? You can use it like this
`(make-combined-string "FSF" "Cambridge")`

Which has exactly the same result.

It’s important to note that procedures created using `lambda` have exactly the same status as the standard built in Scheme procedures, and can be invoked, passed around, and stored in variables in exactly the same ways.

### Alternative definition
Since it is so common in Scheme programs to want to create a procedure and then store it in a variable, there is an alternative form of the `define` syntax that allows you to do just that.
```scheme
;; This define form 
(define (name [arg1 [arg2 …]])
  body …)

;; Is exactly equivalent to the longer form

(define name
  (lambda ([arg1 [arg2 …]])
    body …))

;; So the definition of make-combined-string from earlier could also
;; be done like this
(define (make-combined-string name address)
  (string-append "Name=" name ":Address=" address))
```

While these definitions create functions that accept a fixed number of parameters, know that it is possible to accept a variable number of arguments as well but it'll be covered later.