# Using CPP and Writing Macros for Your C Projects

When you add macros to your code, the C Pre-processor interprets any macros it finds in your source code and replaces, expands or omits portions of text in your source code as you have instructed it to do in those macros. A macro that you are certain to be familiar with is the **#include** macro which tells the macro processor to replace that line with whatever file you put after the directive.

```c
#include <stdio.h> // write the content of the standard io header file here

#define PI 3.14159 // any occurance of the string "PI" will be replaced with 3.1459
#define Square(x) ((x)*(x)) // any occurance of Square(foo) will now be replaced with ((foo)*(foo))

int main(int argc, char *argv[]){
float radius,area;

printf("Enter a radius and I'll give you an area: ");
scanf("%f", &radius);

area = PI * Square(radius);

printf("Radius: %f\n Area: %f\n", radius, area);
  }
```

The second line of this example program uses the "#define" macro to make a definition of the word "PI" that will
same with the Square(x) macro. You also could have multiple arguments in your macro ie(x,y,z,blah). When you do
larger projects in this language, the need for defining a shorthand and conditional replacement of pieces of the
source code become almost essential.... I mean yerrr not gonna rewrite ALL of this crap and maintain it separately
for debug version or other ways you'd need to compile the output. You wanna be able to instruct make and gcc to build
it to spec for you. Same is true in any language, you'd want these features.

# Variable-Length Argument Lists

Some functions that you use in C can accept any length of arguments like _printf()_ and _scanf()_ and really even _main()_
in it's own way that actually kinda makes a lot of sense...

You can actually do this in normal C code and not the way it's done in main with one of the constructs in **stdarg.h** called
**va_list** and it's associated functions.

but it looks like this when you do it with a macro form (pretty similar actually)

```c
#include <stdio.h>

#define Warning(...) fprintf(stderr, __VA_ARGS__)

int main(int argc, char *argv[]){
Warning("%s: this program contains no useful code %d extra number arg \n", argv[0]), 12;

return 0;
}
```

# Non-Syntactic Macros

If you thought about, you could really start to make your C not look like syntactically correct C with macros. You probably
don't wanna do that for the most part but consider these macros and how they might be very useful or just very suck.

```c
#include <stdio.h>

#define NoisyInc(num) (puts("incrementing"), (num++))
#define UpTo(derp) for(i = 0; i < (derp); i = NoisyInc(i))

int main(int argc, char *argv[]){
UpTo(10)
{
printf("oh wanndy... you have derpped now!\n");
}
}
```

So, two expansions are at play here and hopefully it illustrates how bad of a design decision you can make early on when setting
macros that you might pay for later.

**UpTo(10)** in the main function becomes the for loop that it is defined to expand out to. BUT when it reaches the portion of the loop where **i** would normally be incremented with the operator **i++** we have replaced it with the **NoisyInc(derp)** macro which will evaluate both portions of **(puts("incrementing", num++))** and return the furthest most right hand value. meaning for one that the message will be printed and the incremented value of the variable placed in the parentheses will be returned **i++** in our case but it also means that you end up having to write some really retarded looking code
```c 
for(i = 0; i < (derp); i = NoisyInc(i)) 
```
idunno, the equal sign at the end of the conditional doesn't sit well with me plus the syntax of

```c
UpTo(10)
{
printf("oh wanndy... you have derpped now!\n");
}
```
looks weird and you'll run into edge cases like 2 dimensional arrays where implementing this macro gets weird. This is just a real suck idea to use something like this beyond a short piece of code or a demonstration as you see it being used here. It is pretty powerful though and there are times when it makes sense... especially if you have to deal with multiple enclosures of functions that have to accept pointers to functions as arguments that go like 4 layers deep. EFF-THAT.

# The Stringize and TokenPasting Operators

When you wanna address the parameter accepted by a macro as a string, the stringize operator "#" lets the preprocessor know that you mean to replace the occurance of that parameter with the literal string the macro just read without evaluating it ie( "2+2" the string vice the evaluation of the integer operation (2+2) which would resolve to 4)

When you need a variable way of defining function or variable names, the preprocessor can take the token-pasting operator "##" as a hint that the next occurance of a parameter should be expanded in place as a piece of source code and not a string or piece of code that is to be evaluated. Strings and source code are still different things... you wouldn't address sourcecode with a format string like this %s would you?

This example will include a multi-line macro to show you that you can by just using the escape character _"\"_ and demonstrate use of the token pasting and stringize operators.

```c
#include <stdlib.h>
#define DeclareSort(prefix, type)\
static int \
_DeclareSort_ ## prefix ## _Compare(const void *a, const void *b) \
{ \
const type *aa; const type *bb;\
aa = a; bb = b; \
if(aa < bb) return -1; \
else if (bb < aa) return 1; \
else return 0; \
} \
\
void \
prefix ## _sort(type *a, int n)\
{\
qsort(a, sizeof(type), n, _DeclareSort_ ## prefix ## _Compare); \
}

DeclareSort(int, int)

int main(int argc, char *argv[]){
int *a;
int n;
...
int_sort(a, n);
}
```

The macro used above will exapand to the following if DeclareSort is called as it is in the example with (int, int) as it' parameters so.... i got all hung up on the DeclareSort function beginning with an underscore and this doesn't matter at all only the __ double underscore are special to C and more specifically it is significant to the preprocessor. While functions declared in stdlib and other libraries begin with underscores to protect namespaces which allows you to declare functions with the same names without working about the naming collision... it has no bearing on the actual function. this example was taken from a class taught at harvard and probably refletcts some stylistic preferences that make sense to people who have been doing this for a while to differentiate that the function was generated by a macro but idunno... it's work just as fine without the leading underscore.

```c
_DeclareSort_int_Compare(const void *a, const void *b)
{
const int *aa; const int *bb; // make new int pointers
aa = a; bb = b;               // make them point to the address that the void pointers went (effectively casting them to type int*)
if(aa < bb) return -1;        // if int a bigger int b return -1
else if (bb < aa) return 1;   // if int b bigger int a return 1
else return 0;                // if int a == int b return 0
}

void int_sort(int *a, int n)                        // take an int pointer and number of ints in an array
{
qsort(a, sizeof(int), n, _DeclareSort_int_Compare); // perform a quicksort against an integer array, of n items, using  _DeclareSort_int_Compare() to determine which is number is bigger
}
```
learn what [qsort](https://en.wikipedia.org/wiki/Qsort) is

# Metadata
**TAGS:** #Programming