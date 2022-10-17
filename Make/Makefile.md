# GNU Make
The GNU make utility reads a file that tells the compiler [tool chain](Terms.md#^589b8d) the steps needed to pre-proccess, compile and link source files in order to get a working executable.

make is better than just using scripts because it allows you to define the relationships between different elements of your project so that you can more efficiently and effectively put the final product together.

With the make utility, once you are in the root folder of your project directory you should have a text file called _Makefile_ that directs the make utility through the steps it needs to build your project.

for example: you would'nt want to compile parts of the project that are not affected by changes to a library that is not utilized by all components of the project. Especially after the project has gained any level of real length and complexity.

# A Hello World Makefile

While super simple and not any easier than running from the command line this Makefile for compiling a hello world program written in C will be our contrived example of _make's_ use

_Makefile_
```makefile
hello: hello.c
	gcc hello.c -o hello
```
_hello.c_
```c
#include <stdio.h>
int main(int argc, char **argv){
    printf("hello world!\n");
    return 0;
}
```

The first line in the Makefile above defines the default target and if you run make in the directory of this file, it will execute the commands listed under the target.

any targets defined after this one can be run by feeding the target name as an argument to the make command.

# Writing Simple Makefiles
To demonstrate some of the more useful things you can start doing with the _make_ I'll go over some example Makefiles you might want to create for smaller projects. Some of them will require you to use more than the vanilla linking and compiling tool but if you have _flex_ and _yacc_ installed, you should be able to file along.

## The Most Simple Case
in the hello world Makefile in the last section (listed below) these terms are delineated
_Makefile_
```makefile
hello: hello.c
	gcc hello.c -o hello
```

target name: hello prerequisites: hello.c commands: gcc hello.c -o hello

## Dependent Targets
by default, make will run the hello target because it is listed first however… if it sees any rules associated with the prerequisites which don’t exist yet or have been modified more recently than the target, then it will finally rebuild the actual target. This example program and Makefile demonstrate this behaviour

_count_words.c_
```c
#include <stdio.h>

extern int fee_count, fie_count, foe_count, fum_count;
extern int yylex( void );

int main( int argc, char ** argv )
{
    yylex( );
    printf( "%d %d %d %d\n", fee_count, fie_count, foe_count, fum_count );
    exit( 0 );
}
```
_lexer.l_
```lex
        int fee_count = 0;
        int fie_count = 0;
        int foe_count = 0;
        int fum_count = 0;
%%

fee     fee_count++;
fie     fie_count++;
foe     foe_count++;
fum     fum_count++;
```

_Makefile_
```makefile
count_words: count_words.o lexer.o -lfl
        gcc count_words.o lexer.o -lfl -o count_words

count_words.o: count_words.c
        gcc -c count_words.c

lexer.o: lexer.c
        gcc -c lexer.c

lexer.c: lexer.l
        flex -t lexer.l > lexer.c
```

the general syntax for a target definition goes like so…
targt-1 target-2 … target-N : prerequisite-1 prerequisite-2 … prerequisite-N command-1 command-2 command-3

multiple targets can be placed but there cannot be 0 0 or more prerequisites can be placed If you had no commands listed…. the whole thing would be kinda pointless

also: each command must be preceded by a tab… pythonic quirck… I know

## Makefile order
When make reads the Makefile it processes whichever rule is at the top of the file first. It then determines (based on the prerequisites) which targets need to be generated.

example:_Makefile_
```makefile
count_words: count_words.o lexer.o -lfl
        gcc count_words.o lexer.o -lfl -o count_words

count_words.o: count_words.c
        gcc -c count_words.c

lexer.o: lexer.c
        gcc -c lexer.c

lexer.c: lexer.l
        flex -t lexer.l > lexer.c
```

-   count_words tells make check the targets count_words.o and lexer.o
-   count_words.o checks for changes in count_words.c (which it will recompile if needed)
-   make then returns to the first line and sees that lexer.o needs to be checked
-   lexer.o depends on changes in lexer.c which will be recompiled if needed
-   lexer.c will need to be recreated with flex if lexer.l has changed.
-   after all of this has been checked, make links the two object files count_words.o and lexer.o with the gcc command which was the ultimate goal of make the whole time. to generate the count_words elf file.

# Using Rules
The target definitions discussed so far are also called rules and what make does with these rules is determine a dependency graph by comparing the most recently updated files in the project to the prerequisite listings of each rule and then execute each target in accordance with that graph.

## Basic Rule Types
There are 3 basic rule types that you will use in make

-   Explicit rules: the shit we’ve been working with and the most common type of rule you will work with.
-   Pattern rules: use wild cards to list prerequisites or targets (you might wanna run a target any time a file named _derp\_\*\_.c_ is updated)
-   Implicit rules: patter or suffix rules that are defined in the rules database (internal to the make utility)

### Explicit Rules
when you use multiple targets in a rule like below you are affecting the dependancy graphs that make can end up creating by telling it that both vpaht.o and variable.o’s creation depend on the status of change for the header files listed in the rule
```makefile
vpath.o variable.o: make.h config.h getopt.h gettext.h dep.h
```
now, you’ll be able to create rules that know to run the vpath.o rule which happens to be the same as variable.o whenever these header files change which will be needed whenever something that relies on one of those is to be compiled and linked to them.

### Pattern Rules
fuckin…. wild cards and expressions maybe… like this.
```makefile
prog: *.c
	$(CC) -o $@ $^
```

so… compile the .c file that was changed whenever it’s changed.

not sure if it’s obvious to whoever is reading this but wild card expansion is performed per file in the target line but is expanded again in the subshell of each command line that gets executed.

# PHONY targets
PHONY targets label commands to be run without any dependent files.
## Most basic use of PHONY targets
The clean target is a good example of one of the more common uses of PHONY
```makefile
clean:
	rm -f *.o lexer.c
```

make knows to execute this whenever you explicitly instruct it to since there are no prerequisites listed.

however, without being told make will not know the difference between a phony target and a normal one. meaning that if for whatever reason, a file named “clean” was already in the project. running make clean would execute nothing and generate this message

``` $ make clean make: ‘clean’ is up to date. ```

To label the target as a phony one, just declare it below the .PHONY tag like you see below
```makefile
.PHONY: clean
clean:
	rm -f *.o lexer.c
```
## Using Multiple PHONY Tags
For ease of maintenance, you can use multiple .PHONY tags to separate groups of phony targets that you might wanna run.

for example: checking disk space in \*nix system and a Window$ system is done differently but it would be nice to have one target for either case. so you can run a target made for either without changing the command in multiple places.
```makefile
.PHONY: make-docs-linux
make-docs-linux: df
        javadoc ...

.PHONY: make-docs-windows
make-docs-windows: df-windows
        javadoc ...

.PHONY: df
df:
        df -k . | awk 'NR =  = 2 { printf( "%d available\n", $$4 ) }'

.PHONY: df-windows
df-windows:
        win-dir-stat | psgrep 'disk'
```
There are 4 targets here and the benefit isn’t obvious cuz it’s not a huge make file but had you made something with lots of targets that wanna know about disk space on either system but could need to chage the way that it’s checked at a later date, you would only need to change the phonies for df and df-windows. this would cascade the change out to the make-docs targets and any other targets that made use of df. vice doing this in a command where you would need to replicate the change in each of them.

## Using PHONY Targets for Make Output
Since make ends up taking multiple paths as it interprets a makefile debugging can be real shitty so what most makefile authors like to do is output some info about the target that make is executing so that you can know where exactly the thing bombed out.

```makefile
$(Program): build_msg $(OBJECTS) $(BUILTINS_DEP) $(LIBDEP)
        $(RM) $@
        $(CC) $(LDFLAGS) -o $(Program) $(OBJECTS) $(LIBS)
        ls -l $(Program)
        size $(Program)

.PHONY: build_msg
build_msg:
        @printf "#\n# Building $(Program)\n#\n"
```
because build_msg is a PHONY target, it is executed before any prerequisites are checked. otherwise the order of execution all relies on how make cached it’s dependency model for the program over the last run.

all build_msg does of course is print the name of the program going through the build process.

## Empty Targets
empty targets allow you to conditionally execute makefile rules by listing an empty file as a prerequisite. like in the example of doing make clean the wrong way without making clean a PHONY target but to your benefit.

```makefile
prog: size prog.o
        $(CC) $(LDFLAGS) -o $@ $^

size: prog.o
        size $^
        touch size
```
The first time you run make, the size rule will run because the “size” file does not exist yet. but any time after that size will not run until prog.o is updated. even if you update the “size” file.

# Variables in Make
Variables can be assigned in two ways just like in c where you can pass a reference or a value to the variable and in the make world these are called recursively expanded and simply expanded variables.

## Classes of Variables and How to Define Them
**Simple expansion**
```makefile
	foo := derp bar := $(foo) 
```
at this point _\$(foo)_ and _\$(bar)_ both yield _derp_ 
```makefile
foo := blah 
```
at this point the values will be different because this is
**Recursive expansion** 
```makefile
foo = derp bar = $(foo) 
```
at this point _\$(foo)_ and _\$(bar)_ yield _derp_ 
```makefile
foo = blah  
```
at this point they both yield _blah_ (similar to passing by ref)

Variables in make will be addressed with any of these syntaxes. _\$(VariableName)_ _\${VariableName}_

## Make's Built-in Variables
your makefiles can make use of special variables that are defined dynamically as make is processing your makefile. This keeps you from having to come up with clever tricks that you would eventually just forget when trying to address things like the currently processed target and some of it’s prerequisites.

-   $@: the file name of the current targets
-   $<: the file name of the first prerequisite
-   $?: the name of all prerequisites that are newer than the target
-   $^: file names of all prerequisites in the makefile listed uniquly and separated by spaces
-   $+: the name of all prerequisites separated by spaces and with duplicates listed if they happen to be there (not uniq)
-   \$\*: the stem of the target file (target file name without the suffix)
    
    any of these variable names followed by a ‘D’ tells make that you are referring to the directory portion of the variable name only
    

The makefile used in the count_words example can be done completely with automatic variables
```makefile
count_words: count_words.o counter.o lexer.o -lfl
        gcc $^ -o $@ # all the prerequisites in the rule will be linked

count_words.o: count_words.c
        gcc -c $< # the first prerequisite file will be compiled

counter.o: counter.c
        gcc -c $< # the first prerequisite file will be compiled

lexer.o: lexer.c
        gcc -c $< # the first prerequisite file will be compiled

lexer.c: lexer.l
        flex -t $< > $@ # flex will be run against the first prerequisite and
						  # send the output to a file with the name of the target
```

# Using VPATH and vpath to work with Hierarchies of source file
So far our the projects have been fairly simple and not split across too many files or between different directories.

## The VPATH variable
VPATH is a variable that can be set as a list of directory paths relative to where the makefile is run from by the make utility

example:
```makefile
    VPATH = src resources derp-files include
```
This lets make know that if it doesn't see a prerequisite file in it's directory that it should look int ./src ./resources ./derp-files and ./include

## The vpath varaible
The vpath all lowercase variable allows you to let make know where to look for files that you define as patterns. This allows you to make decisions that are a little more fine grained and that take less time to build.

example:
```makefile
vpath %.c src
vpath %.l src
vpath %.h include
```

This would let make know to include the paths of:
```output
all *.c files in ./src
all *.l files in ./src
all *.h files in ./include
```
## Setting Include paths to be used by the compiler
The example makefile in this lesson makes use of the include paths and many c/cpp projects will do this because users will have libraries to be included by the project on their machine.

It looks like the below

```makefile
VPATH = src
CPPFLAGS = -I include

count_words: count_words.o counter.o lexer.o -lfl
	gcc $^ -o $@

count_words.o: count_words.c include/counter.h
	gcc $(CPPFLAGS) -c $<

counter.o: counter.c include/counter.h include/lexer.h
	gcc $(CPPFLAGS) -c $<

lexer.o: lexer.c include/lexer.h
	gcc $(CPPFLAGS) -c $<

lexer.c: lexer.l
	flex -t $< > $@

.PHONY: clean
clean:
	rm *.o *.c
```
What's happening here is the commands reference the _\$(CPPFLAGS)_ variable. This leverages the way the _gcc_ command works. In this case the **-I** flag can be followed by a list of folders that _gcc_ will search through for _\*.h files_ (include files) which allows the target to be created each time.

# Pattern rules
Stuff we've done so far have been small enough that writing a makefile that serves every file isn't too big of a deal but soon it would become too much to maintain so many makefile authors write pattern rules

## Make  built in pattern recognition
on it's own, make actually knows things like all .c programs resolve to .o files and use the COMPILER listed in your environment variables. this allows us to reduce the makefile from the chapter 3 example like so.

```makefile
VPATH = src
CPPFLAGS = -I include
count_words: count_words.o counter.o lexer.o -lfl
	gcc $^ -o $@
count_words.o: count_words.c include/counter.h
	gcc $(CPPFLAGS) -c $<
counter.o: counter.c include/counter.h include/lexer.h
	gcc $(CPPFLAGS) -c $<
lexer.o: lexer.c include/lexer.h
	gcc $(CPPFLAGS) -c $<
lexer.c: lexer.l
	flex -t $< > $@
.PHONY: clean
clean:
	rm *.o *.c
```
down to

```makefile
VPATH    = src include
CPPFLAGS = -I include

count_words: counter.o lexer.o -lfl
count_words.o: counter.h
counter.o: counter.h lexer.h
lexer.o: lexer.h

.PHONY: clean
clean:
	rm *.o
```
### Setting the default target
- make sets the default goal to produce the target count_words
- make identifies 4 prerequisites because from the dependancies
  (count_words.o which was detemined using a builtin rule) counter.o,
  lexer.o and the -lfl flag

### The count_words.o prereq
- make then goes on to satisfy the first prereq by following the count_words.o
  target
- make then follows another implicit rule that tells it that target.o gets
  created by running target.c through the compiler which it is able to find
  count_words.c using the VPATH variable and is able to now update count_words.o

### The counter.o prereq
- make follows the next prereq in line for the default target (counter.o)
- make uses the implicit rule for generating .o targets by compiling .c targets
  again

### The lexer.o prereq
- make uses the implicit rule for generating .o targets again but can't find
  lexer.c even by searching the VPATH directories
- make is smart enough to assume this is an intermediate file and tries the
  implicit rule for generating .c file from .l files using the lexer "flex" in
  this case
- Now that lexer.c does exist, it can use the rule for making .o files to finish
  creating lexer.o

### The -lfl flag
- the -lfl flag tells the compiler command line from the default target to link
  to the libfl library which the system can locate for make at /lib/libfl.a


The builtin rules that make this possible look like this

```makefile
%.o: %.c
	$(COMPILE.c) $(OUTPUT_OPTION) $<
%.c: %.l
	@$(RM) $@
	$(LEX.l) $< >$@
%: %.o
	$(LINK.o) $^ $(LOADLIBES) $(LDLIBS) -o $@
```
- compile .c files and apply any output options
- remove .c files that were generated by the lexer and make a new one
- link all .o files with any library loading options and outpu the target name
## Pattern rule syntax
The '%' sign in pattern rules is similar to '\*' in the shell and acts as a wild card in makefiles but can only be used once per unit. Meaning per target or per prereq.

examples below
```makefile
%: %.mod
        $(COMPILE.mod) -o $@ -e $@ $^

%: %.cpp
        $(LINK.cpp) $^ $(LOADLIBES) $(LDLIBS) -o $@

%: %.sh
        cat $< >$@
        chmod a+x $@
```
### in the first line we declare:
- any target with _\<anything\>_**.mod** as a prereq is fulfilled by
- running the gcc command used for mod files
- with the -o option (for naming output files) as $@(the target name) with the -e command (whatever the fuck) $@ (the target name) $^ (all of the .o files in the project)

### in the second line
- anything that has _\<something\>_**.cpp** as a prereq
- will use the cpp linker against ALL *.o files and libraries
- and output the target name

## Static pattern rules
Static patern rules are just pattern rules that instaed of applying to a wild card list of rules like (any _\*.c_ file)

These rules apply to a list of targets in this example the list is contained in the variable called $(OBJECTS)

Static pattern rules follow this syntax structure.
```<targets>...: <target-pattern> : <prerequisites-pattern>
    commands...
```

```makefile
$(OBJECTS): %.o: %.c
	$(CC) -c $(FLAGS) $< -o $@
```
### The example above declares
- if the target name was in the definition of $(OBJECTS)
- append .o to the target's name.
- make the target-name(stem).c the prerequisite to the target
- compile (gcc -c) with defined flags $(FLAGS) the prereq (target.c) and make the output the name of the target which would just be (stem).o

## Suffix pattern rules
Suffix patterns are not typically used and can be replaced by static pattern rules. They exist in the makefiles of some code though.

They define actions taken to make targets from just the suffixes of files.

you'll see what i mean.

```makefile
.c.o:
	$(COMPILE.c) $(OUTPUT_OPTION) $<
```
This will tell make that any .c file will produce a target with the suffix .o by running the compiler with some output options against the listed prerequisite

if this suffix rule is defined....
```makefile
counter:counter.c
```
would tell make to create counter.o by compiling counter.c

```makefile
%.o: %.c
	$(COMPILE.c) $(OUTPUT_OPTION) $<
```
maybe this is obvious but you can make suffix rules that apply only one suffix
type.

```makefile
p.:
	$(LINK.p) $^ $(LOADLIBES) $(LDLIBS) -o $@
```
This rule tells make to compile .p files with  the pascal compiler and just produce the target with no extension.

it's fully analogous to this rule

```makefile
%: %.p
	$(LINK.p) $^ $(LOADLIBES) $(LDLIBS) -o $@
```
# Implicit Rules
GNU/Make comes with about 90 builtin rules that support mostly c,cpp,yacc and some documentation frameworks like latex

Implicit rules are invoked by declaring a target without specicying a command script

mostly this works fantastically but it can still fuck you over because rules have to be based on patterns and some of the file name patterns overlap think
(_lisp_ \*.l files and _flex_ \*.l files)

if you have this problem, you can remove the rule for flex files from the database to keep make from trying to make object files from \*.l files like this.

```makefile
%.o: %.l
%.c: %.l
```
all this would do is tell make to do nothing in the case of these implicit rules as there are no command scripts under them.

## How make Determines which implicit rules to use when building a target
when make is searching for the appropriate implicit rule to generate a target
- It reads the target name and matches that to a pattern in it's rules database

- then it matches the prerequisite pattern against the subset of rules that it matched against the target in the last step

- then it searches the folder paths it knows about for the actual prerequisite file (\*.c \*.l or whatever source file)

- if it finds the prerequisite, make will begin updating the target if it needs to

- if it can't find the source file make will assume that the prerequisite it has not found is a target that has not been created yet and search for a source file that matches an implicit rule that would generate the prerequisite that it needed.

- performing this search as a recursive action in the implicit rule used in the original target that make was processing allows it to chain together actions to finish the build process without the need for putting a rule to each file explicitly

## How make used implicit rules in our example reduced makefile
It's actually doing this in one of the previous examples

```makefile
VPATH    = src include
CPPFLAGS = -I include

count_words: counter.o lexer.o -lfl
count_words.o: counter.h
counter.o: counter.h lexer.h
lexer.o: lexer.h

.PHONY: clean
clean:
	rm *.o
```
where _lexer.o_ is the target
- make sees **lexer.o: lexer.h** which matches this rules
  ```makefile
  %.o: %.h
    gcc -c  -I $(VPATH)/ $*.c -o $@
  ```
  or something like that (compile stem.c and include the \*.h file then output the target)

- make would need to find lexer.c in the VPATH and doesn't

- so it assumes that lexer.c is a target that it can create

- make begins to search for files that are known to make .c files that are named
  lexer.something

- it sees lexer.l in the path somewhere and runs the implicit rule
```makefile
  %.c: %.l
    flex $< > $@
```
or somethin like that (use flex to make a \*.c file from a \*.l file)

- then make returns to the rule that started this side-quest except now it has
  the lexer.c file that it needed and is able to perform the compilation to
  generate the lexer.o target that it needs to reach it's eventual goal.

## Structure of implicit rules
The implicit rule used for object file targets looks like this

```makefile
%o: %.c
	$(COMPILE.c) $(OUTPUT_OPTION) $<
```
This rule can be customized by changing values of it's constituent variables. and by default they are set like this.

```makefile
COMPILE.c = $(CC) $(CFLAGS) $(CPPFLAGS) $(TARGET_ARCH) -c
CC = gcc
OUTPUT_OPTION = -o $@
```
CFLAGS         => compilation optoins
CPPFLAGS       => preprocessor options
TARGET_ARCH    => type of hardware/os

This set up makes it easy for you to change the way make will execute implicit rules in your project.

You can set these variables to the values you need at the top of the makefile or, in a separte file. The suckless community seems to like putting these settings in a file called config.mk by adding this line to the top of the project's makefile.

```makefile
CONFIGFILE = config.mk
include $(CONFIGFILE)
```
**Cool side-note**
- make built in version control
- make knows how to use RCS which is some boomer era version control system. I think it knows how to use css as well
- make has (what-if) mode running ```make -n foo``` in a folder with the foo project in it will cause make to let you know which rules it would employ and which steps it would take to produce the target "foo" in that directory.
	keep in mind that it wouldn't have to be the final product "foo" in this case, you could also run it against something like foo.o "make -n foo.o"
	
## Special Targets
Besides .PHONY there are other phony targets that can be used in make that give you some more control over the algorithms used by make to determine how prerequisites and targets will be dealt with like (when targets will be considered out of date, which intermediate files need to be generated in certain situations and shit like that). They will be tabled down below.

<table style="border-collapse:collapse;border-spacing:0" class="tg"><thead><tr><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Tag Name</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Description</th></tr></thead><tbody><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">.INTERMEDIATE</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Tells make that prerequisites listed under this target are to be considered as intermediate and<br>should be deleted as make exits. (super useful in our example where flex makes lexer.c which is<br>only needed for the obj file</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">.SECONDARY</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Tells make that the prerequisites listed are created intermediatly but are not to be deleted</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">.PRECIOUS</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Tells make that the prequisites are never to be deleted even if make is interrupted while running</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">.DELETE_ON_ERROR</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Tells make that if it encounters any error with the listed prerequisites during the build process<br>that it needs to delete the file.this is the opposite of the .PRECIOUS target</td></tr></tbody></table>

# Managing Libraries and Dependancies
Now that the source file has been split across multiple include files and source files whenever an update to the included sources changes each file that lists the include statement should actually be updated too and since those statements don't all exist in the same file anymore, We'll have to list them as dependancies.

gcc on it's own can search and list these dependencies for you with the -M flag

```sh
gcc -M count_words.c
```
for example would generate a long list of full paths to include files used by stdio and whatever else was imported in the project

This could be automated with a big gross command script inside a pattern rule

```makefile
VPATH    = src include
CPPFLAGS = -I include

SOURCES  = count_words.c \
           lexer.c       \
           counter.c

count_words: counter.o lexer.o -lfl
count_words.o: counter.h
counter.o: counter.h lexer.h
lexer.o: lexer.h

include $(subst .c,.d,$(SOURCES))

%.d: %.c
        $(CC) -M $(CPPFLAGS) $< > $@.$$$$;                      \
        sed 's,\($*\)\.o[ :]*,\1.o $@ : ,g' < $@.$$$$ > $@;     \
        rm -f $@.$$$$
```
- to make \*.d targets with \*.c prerequisites
- run gcc with the -M option against the prereq
- send the output to target.d.\$\$\$\$
- use sed to replace stem.o with the target name all while placing each in a colon separated list causing the output to look like (counter.d: counter.c) but for each dependency listed from gcc -M
- remove the temp .\$\$\$\$ file that was used for formatting

 the combination of the include _\$(subst .c,.d,\$(SOURCES))_ line and this line causes make to resolve targets for each .c file with each affected library attached to the include directives in their code.

C libraries can be organized into collections of object files that can be referenced into programs that list them in their include path.

The objects that are linked into this arrangement are called members and the arrangement itself is called an archive and are suffixed with a **.a**

## Compiling Statically Linked Archives in Your Project
In this chapter we will re-arrange our count_words program into an archive.

archive files are created and managed with the ar command.

```bash
ar rv libcounter.a counter.o lexer.o
```
the command instructs the shell to archive by replacing members in the archive with the members listed in the command and to do it verbosely.

**note:** this won't remove any members that are not listed and the output will show an r - obj or a - obj for members that were replaced or added respectively

```bash
cc count_words.o -L. -lcounter -lfl -o count_words
```
in the above line, the compiler/linker is told:
- count_words.o: is to be linked
- L.: Library or archive files should be found in "." the current dir
- lcounter: link the archive called libcounter.a
- lfl: link the archive (shared obj i think) in this case called libfl.so from wherever the user's libs are kept
- o count_words: produce an output called count_words that is executable

  so, had libcounter.a been moved to /usr/lib somewhere

the examples from chapter 4 where you have source and include files separated across a couple directories can actually be compiled against a static archive like this by using this makefile

```makefile
VPATH = src

ARCHIVE_MEMBERS = counter.o\
		  lexer.o

LIBS = -lcounter\
       -lfl\

CFLAGS =  -L.
CPPFLAGS = -I include

count_words: libcounter.a count_words.o
	$(CC) $@.o $(CFLAGS) $(LIBS) -o $@

libcounter.a: $(ARCHIVE_MEMBERS)
	$(AR) $(ARFLAGS) $@ $^

count_words.o: count_words.c
counter.o: include/counter.h include/lexer.h
lexer.o: include/lexer.h

testfile:
	echo "fee fee fee fie fie foe foe foe fum fum fum fum fum" > testfile
.PONY:
clean:
	rm *.o *.a testfile
```
The portion that keeps libfl.a updated was left out of this makefile but I will leave that as an exercise for you to develop one. Keep not that $(SOURCES) is not defined in this makefile.

The archive target and the final target can actually leverage some of makes implicit rules too. you'd just use the following pattern
```makefile
libcounter.a: libcounter.a(lexer.o) libcounter.a(counter.o)
```

the rule will archive all of the members listed in parenthesies from the prerequisites and it will do it in one instance of the ar command which makes this faster if you know ALL of the prereqs will need to be replaced or created.

A pattern rule which looks much more elegant but not as efficent as a well placed implicit implementation of the rule would be written like this

```makefile
libcounter.a: $(OBJECTS)
	$(AR) $(ARFLAGS) $@ $?
```
the \$? automatic variable tells make to place only the prerequisites that have a more recent modified date than the target itself.

below is a reduced version of the makefile used to build the example from this chapter.

in this case, it's the way to go because of the low level of complexity and members involved in this project.

```makefile
VPATH = src include
CPPFLAGS = -I include

count_words: libcounter.a -lfl
libcounter.a: libcounter.a(lexer.o) libcounter.a(counter.o)
count_words.o: counter.h
counter.o: counter.h lexer.h
lexer.o: lexer.h
```
## Prerequisite Order Can Matter
Yeah and one way you'll see this at first is pattern rules. To show you what I
mean

```makefile
count_words: count_words.o -lcounter -lfl
	$(CC) $^ -o $@
libcounter.a: libcounter.a(lexer.o) libcounter.a(couter.o)
```
This will tell you that -lcounter is needed by count_words, meaning that it did not expand the -l option and that's because as make was determining how to generate targets, it had first encountered the rule libcounter.a as a target which became the way that make would address that archive file and why -lcounter would not be able to refer to libcounter.a anymore

While make ends up executing targets from bottom up, prerequisites are called in order from left to right per target and preserve their order across any automatic variables you might use like \$^,\$< or \$? and this is fine too except
in cases where symbols generated in the compilation phase of an object refer to symbols that have not yet been generated

**for example:**
- libraryA.a calls func1 which is defined in libraryB.a
- the link comman MUST list libraryA before libraryB.a -lA -lB (a depends on B)
- make will not look back at B if you listed it before A

**Now suppose:**
- libraryB.a is changed and now references a symbol defined in libraryA.a
- the 2 libs now refer to sections of each other. (a circularity exists)
- now we need to link symbos in B to A as well as from A to B
- the solution is to list both orders -lA -lB -lA

**This affects your pattern rules:**
- Now the automatic variables you choose in your linking commands cannot remove duplicates without breaking a dependancy between one of the libraries and you should use an automatic variable that preserves them
- such as \$+ (all prerequisites with duplicates)

example:
```makefile
xpong: xpong.o libui.a libdynamics.a libui.a -lX11
	$(CC) $+ -o $@
```
will run this for you:
```sh
gcc xpong.o libui.a libdynamics.a libui.a /usr/lib/X11R6/libx11.a -o xpong
```
This way, libui.a can use symbols from libdynamics.a and libdynamics.a can use symbols from libui.a

# Variables and Macros
Make can be thought of as 2 sort of languages, one for describing dependancy graphs (The rule language) and another for making textual substitutions (The Macro language). Other macro languages you're probably familiar with might be the C-PreProcessor, LaTeX and m4.

## Variables
Below some example variable definitions are listed to show you the syntax, and you've seen most of this before

```makefile

#constants
CC := gcc
MKDIR := mkdir -p

#internal variables
sources = *.c
objects = $(subst .c,.o,$(sources))

#functions
maybe-make-dir = $(if $(wildcard $1),,$(MKDIR) $1)
assert-not-null = $(if $1,,$(error Illegal null value.))
```
in the example above you will notice 2 different types of assignment that are happening. they are denoted by
':=' and simply '=' these are called simple and macro expansion respectively and let make know to treat the
variables and assinged values diefferently in this way.

<table style="border-collapse:collapse;border-spacing:0" class="tg"><thead><tr><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Operator</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Expansion Type</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Meaning</th></tr></thead><tbody><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">:=</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Simple Expansion</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">The variable takes the current value of whatever is on the right side similar to assingment by value in C (var = val)</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">=</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Recursive Expansion</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">The variable follows the value of whatever is on the right side similar to assignment by reference in C (var = &amp;val)</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">?=</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Conditional Assignment</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">The variable is only assigned the value on the right side if it has not already been given a value</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">+=</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Append Assignment</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Appends the value on the right side to the values already present in the variable</td></tr></tbody></table>

You can already probably imagine how this matters A LOT and gives you some nice flexibility with what you can do in make.

Examples of this might be
- $(LS) := ls -l
... _make files and dirs and crap_
now \$(LS) will NOT match the output of running the ls -l command in the current directory.

- had you assigned it like this
$(LS) = ls
then it would match

a pretty obvious but easy to overlook caveat with the append assignment operator is that recurisvely expanded variables cannot reference themselves because this renders and infinite loop.... look at this

```makefile
something = value1 value2 $(some var)
something += derp
```
that second line tries to do something like this.
```makefile
something = $(something) derp
```
which causes _\$(something)_ to expand but it's on the right side already so it's gonna expand in place to
```makefile
something = [something] = [something] ... forever ... derp
```
The heuristic here being.... IF YOU WANNA APPEND VALUES TO IT USING += THEN DON'T DEFINE IT RECURSIVELY!

## Macros
Variables work fine for single commands and save us a lot when they need to expand to sizes and complexities that we would never use on the command line.

Sometimes though... you need a sequence of commands to be repeated in a script and this is where you'd first run into the need for a macro.

### Declaring a macro
Macros are assigned to a name using the "define" key word and can span multiple lines. then it's closed off with the "endef" keyword to tell make that
whatever you're doing next is no longer part of this macro.

an example macro that might be useful as a canned sequence for a makefile that deals with building a java archive might look like this.
```makefile
define create-jar
@echo Creating $@...
$(RM) $(TMP_JAR_DIR)
$(MKDIR) $(TMP_JAR_DIR)
$(CP) -r $^ $(TMP_JAR_DIR)
cd $(TMP_JAR_DIR) && $(JAR) $(JARFLAGS) $@ .
$(JAR) -ufm $@ $(MANIFEST)
$(RM) $(TMP_JAR_DIR)
endef
```
it's pretty important here not to tab cuz make is gonna tab the commands out on it's own each time you call this macro in a target.

calling it is pretty simple and here are 2 example of doing that with this macro one with the output of the commands supressed.

__Verbose___
```makefile
$(UI_JAR): $(UI_CLASSES)
$(create-jar)
```
__Suppressed__
```makefile
$(UI_JAR): $(UI_CLASSES)
@$(create-jar)
```
notice the @ before $(create-jar) in the supressed version... it's the only difference.

### Order of Expansion

Make completes it's task in 2 phases

 **Phase 1**
- The makefile and any included '\*.mk' files are read
- variables and rules are loaded into make's internal database
- a dependency graph is created from the files

**Phase 2**
- make analyzes the dependency graph and modify times of files
 it has found in the project.
- it decides which files need to be updated (which targets to run)
- executes the command scripts from bottom to top of the listed dependencies that make knows about.

The table below describes the rules for when make decides to expand a variable or macro.

<table style="border-collapse:collapse;border-spacing:0" class="tg"><thead><tr><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Definition</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Expansion of left side</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Expansion of right side</th></tr></thead><tbody><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">a = b</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Phase 1</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Phase 2</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">a ?= b</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Phase 1</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Phase 2</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">a := b</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Phase 1</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Phase 1</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">a += b</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Phase 1</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">2 if recursive</td></tr></tbody></table>

In the case of macros, their names are determined in phase 1 and the commands they contain are not expanded until the second phase.

## Target and Pattern temporary variables

Since the variables in a makefile are expanded upon the first phase of make's execution an issue that may arise is that you might need to add some items to a variable but ONLY in the case of SOME of  the targets like this example.

gui.o needs to be compiled with the pre-processor flag "-DUSE_NEW_MALLOC=1" but none of the other targets should run with this option.

you could duplicate the command and just add the option for that one target like this

```makefile
gui.o: gui.h
$(COMPILE.c) -DUSE_NEW_MALLOC=1 $(OUTPUT_OPTION) $<
```
but if you need this done for a pattern of files or across multiple, you can probably appreciate just how complex or maybe impossible that can get. so use a target or pattern specific variable like this instead.

```makefile
gui.o: CPPFLAGS += -DUSE_NEW_MALLOC=1
gui.o: gui.h
```
this lets you set whatever extra options you need for the target but still leverage make's implicit rules

keep in mind that it doesn't NEED to be the += assingment operator... all of them are valid when setting these temporary variables.

## Places where variables can be set

**In the makefile or included .mk files**
- These follow the precedence rules you have learned thus far

**On the command line**
- The user can set values to variables from the command line and it looks like this
```bash
 $ make CFLAGS=-g CPPFLAGS='-DBSD -DDEBUG'
``` 
- Assignment from the command line overrides any environment variables and anything in the makefile
- users may specify recursive '=' or simple ':=' expansion operations in the command line

**The environment**
- Make is aware of all of your environment variable settings and they are available in any makefile
- They have the lowest precedence
- You can give them the highest precedence using this option on the command line "-e" or --environment-overrides
- your makefile can export any variables you want by using the "export" command


## Conditional and Include Processing

Like C's preprocessor macros, make gives you the ability enact certain rules depending on whether
or not variables have a known value or whether or not they have been defined at all.

The conditional operators
<table style="border-collapse:collapse;border-spacing:0" class="tg"><thead><tr><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Key Word</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Meaning</th></tr></thead><tbody><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">ifdef</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">If defined</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">ifndef</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">If not defined</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">ifeq</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">If not equal to</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">ifneq</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">If not equal to</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">else</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">More if inline</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">endif</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">End of an if statement</td></tr></tbody></table>

An example of what it looks like when these are used.
```makefile
# COMSPEC is defined only on Windows.
ifdef COMSPEC
PATH_SEP := ;
EXE_EXT  := .exe
else
PATH_SEP := :
EXE_EXT  :=
endif
```
These conditional processing directives can be used in the head of the makefile, in macros or in the command scripts of your targets.

The syntax for the "ifeq/ifneq" directives can be either of these 2 ways

```makefile
ifeq (a, a)
# these are equal
endif

ifneq "a" "a"
# thes are not equal
endif
```
The other directive that, you should already know as we have used it in several of the examples so far is the "include" directive which just tells make to include the source of another makefile in place where you put the directive.

some interesting things you didn't know about this directive....

if an included makefile does not exist, you can create it as a target and make will execute that file after it has been created.

```makefile

include foo.mk
$(warning Finished reading include line)

foo.mk: bar.mk
m4 --define=FILENAME=$@ bar.mk > $@


#### bar.mk
$(warning Reading FILENAME)

```
When you run the command
- you'll first see an error because foo.mk could not be found
- m4 will replace the word FILENAME with $@ (foo.mk) in bar.mk
- that text will be sent to a new file called foo.mk
- foo.mk will issue the message "Reading foo.mk"
- make will report that foo.mk is up to date and exit

you can tell make to not care so hard when it DOESN'T see your include file by placing a dash next to the directive
like this.
```
-include derp.mk
```
Another think to keep in mind when you use includes is that they are source dropped in place where you put them in the
file so, this can screw up your targets by changing the default if you are just unaware that the makefile you are including
has targets defined in it that you do not want to be moved to the top of the graph.

## Standard make variables

Make has some built in variables just like it has built in rules. They are listed in the table below with their description and the
command line argument that you can use to adjust them from the command line if it has one.

<table style="border-collapse:collapse;border-spacing:0" class="tg"><thead><tr><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Variable Name</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Command Flag</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Meaning</th></tr></thead><tbody><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">MAKE_VERSION</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal"></td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">The version of make you are running</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">CURDIR</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">-C or -I</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">The directory the make process is executing from(-C). It also contains include dirs (-I)</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">MAKEFILE_LIST</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal"></td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">The list of all files make has read at the point that this variable is used</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">MAKECMDGOALS</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal"></td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">The command line options set for the referenced instance of make</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">.VARIABLES</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal"></td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">The list of ALL variables read into make so far with the exception of the target</td></tr></tbody></table>

The Acation variables are there as well and their use is best described by this graph of which action is used for
which target.

![](Pasted%20image%2020210911161154.png)

# Functions
Functions in gnu make are defined in the same way that macros are “between define and endef keywords”. The difference in them functions and macros in make is how you can use them. First of which is the use of function arguments.

Arguments to functions are addressed in the definition similar to the way they are in shell scripts “the \$1,\$2,\$3…\$n notation” and later called in the makefile using the “call” function with this syntax.
```makefile
$(call func-1 arg1,arg2,…argN)
```

The call function in make does’nt actually transfer control to a function like it would in other programming language but just tells the make program to write the macro in place with the arguments substituted with the arguments placed between commas in the function call.

This chapter will cover some subtleties involved with this implementation as well as some of gnu/make’s built in functions.

## User Defined Functions
an example of a user defined function that just uses awk to show all processes owned by the user placed in the argument portion “emmy” in this case will be printed to the screen.

```makefile
PS		:= ps
AWK	:= awk

PSOPTS	:= aux
FIELDS	:= $$1 $$3
FW		:= "10 57 100"

define my-func
	$(PS) $(PSOPTS) | \
	$(AWK) 'BEGIN { FIELDWIDTHS = $(FW)} \
	/$1/ {\
		print $(FIELDS)\
	}'
endef

do_func:
	$(call my-func,emmy)
```
This function definition will allow you to use awk to parse out the username and process path for any user in the first argument position.

Notice the definition of the FIELDS variable contains a double ‘\$’ character. This is how the call function in make knows to escape the character from it’s own shell and apply it only to the subshell.

Both make and awk address variables in this way and the double ‘\$’ just places the text into the executing subshell and removes the first instance of the ‘\$’ so that it may be used by the calling program. You’d probably just add more for each level deeper you’d need to pass a variable to a subshell/subfunction

## Built in Function
Because you will run into situations where complex text manipulations will need to be done against variable content to create your targets, gnu make comes with some pre-defined functions for just these sorts of tasks and they will be covered in this section.

<table style="border-collapse:collapse;border-spacing:0" class="tg"><thead><tr><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Name</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Syntax</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Example</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Description</th></tr></thead><tbody><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">filter</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(filter pattern...,text)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(filter ui/%.o,$(objects))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns only the strings in $(objects) that match the pattern "ui/%.o"</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">filter-out</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">filter-out | $(filter-out pattern...,text)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(filter-out %.h,$(all_source))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns only the strings in $(all_sources) that DO NOT match the pattern %.h</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">findstring</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">findstring | $(findstring string,text)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(findstring debug,$(PWD))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns the word "debug" if the output of pwd contains that word</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">subst</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(subst search replace,text)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(subst .c,.d,$(SOURCES))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns the text of $(SOURCES) with ".c" replaced by ".d" for each workd</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">pathsubst</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(patsubst search-pattern,replace-pattern,text)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(patsubst,%/,%,$(dir-paths))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns all patterns ending in '/' with only the matched pattern.</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">words</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(words text)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(words,$(my_list))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns the number of words in $(my_list)</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">word</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(word n text)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(word 5,$(my_list))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns the 5th word in $(my_list)</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">firstword</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(firstword text)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(firstword,$(my_list))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns the first word in $(my_list)</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">wordlist</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(wordlist start,end,text)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(wordlist 3,5,$(passwd))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns the list from items 3 to 5 in the varaible $(passwd)</td></tr></tbody></table>

**Note:**
All of these functions are called with a leading white space before the argument list and commas there-after. This is important because the commas delimit the list and white space will be considered part of the argument if it is listed.

Another thing to note about all string functions is that they have no concept of where a file name starts or end, just the strings the arguments you give them 

**for example:**
\$(subst .c,.o,\$(SOURCES))
will perform substitutions that you might want like (hello.c => hello.o) but it will also perform some substitutions that you might not want like (hello.cdr.c => hello.odr.o)

### Misc Functions
<table style="border-collapse:collapse;border-spacing:0" class="tg"><thead><tr><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Name</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Syntax</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Example</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Description</th></tr></thead><tbody><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">sort</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(sort list)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(sort $(names))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns the list $(names) in lexigraphical order</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">shell</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(shell command)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(shell echo "derp")</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns the output of the shell running echo "derp"</td></tr></tbody></table>

**Some details on the shell function**
When the shell function returns it's output to make, all of it's newline characters are stripped and so is the stderr output stripped

### Filename and Directory Functions
<table style="border-collapse:collapse;border-spacing:0" class="tg"><thead><tr><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Name</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Syntax</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Example</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Description</th></tr></thead><tbody><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">wildcard</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(wildcard pattern...)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">sources := $(wildcard *.c *.h)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Set sources to the list of all .h and .c files</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">dir</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(dir list)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(dir $(shell find . -name '*.c'))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns only the directory name of all dirs that contain *.c files</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">notdir</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(notdir list)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(notdir $(shell find . -name '*.c'))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns only the filename portion of all *.c files found</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">basname</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(basename list)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(basename $(sources))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns only the stem of each filename in the list</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">addsuffix</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(addsuffix suffix,name)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(addsuffix /*,$(prjct_folders))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns all items in $(prjct_folders) followed by "/*"</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">addprefix</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(addprefix prefix,name)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(addprefix $(path),$(progs))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns all items in $(progs) with the content of $(path) in front of it</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">join</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(join prefix-list,suffix-list)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(join $(newpath),$(old_progs))</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Returns a list of the items from $(newpath) and $(old_progs) that is the cross product of the two</td></tr></tbody></table>

Wildcard will return the list of all files reletive to the current directory that have been specified by the pattern and supports all of the normal file-globbing characters "~, \*, ?, \[...\], \[^...\]"

The join function against something like (one- two- three-) and (1 2 3) will look like this (one-1 two-2 three-3) they will sort of be interlaced with eachother

### Flow Control
This section will take a little bit of a different form to more properly demonstrate the use of the flow control functions as their examples will require the use of multiple lines

<table style="border-collapse:collapse;border-spacing:0" class="tg"><thead><tr><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Name</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Syntax</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Description</th></tr></thead><tbody><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">if</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(if condition,then-part,else-part)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Runs then if the condition is true and else if it was not</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">error</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(error text)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Prints the error text and exits make with status code of '2'</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">foreach</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$(foreach,variable,list,body)</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Iterate over a list and execute body in each iteration using variable as the counter</td></tr></tbody></table>

#### Using if
The if function presents make with two variable or macros for expansion and uses the condition to decide which of the two to expand. What this ends up looking like is depicted in the example below.

```makefile
PATH_SEP	:= $(if $(COMSPEC),;,:)
```
This would set the varaible "PATH_SEP" to ';' if $(COMSPEC) was definded
- usually in the $PATH and only ever in windows if $(COMSPEC) is not defined, the expansion for ':' is performed which would be the correct PATH_SEP value to use in a \*nix system.

#### Using error
You're most likely to use this in conjunction with an if function in this sort of way.

```makefile
# $(call assert,condition,message)
define assert
  $(if $1,,$(error Assertion failed: $2))
endef
# $(call assert-file-exists,wildcard-pattern)
define assert-file-exists
  $(call assert,$(wildcard $1),$1 does not exist)
endef
# $(call assert-not-null,make-variable)
define assert-not-null
  $(call assert,$($1),The variable "$1" is null)
endef
error-exit:
        $(call assert-not-null,NON_EXISTENT)
```
This example defines a general assert function that lets you expand into several more specific uses of the macro with different error messages attached to them which is a cleaner way of handling the conditions.

#### Using foreach
Kinda like ansible does with jinja templates, make can expand text in repeated loops using it's own syntactic method. To depict what this kinda looks like for you... take a look below.

```makefile
letters := $(foreach letter,a b c d,$(letter))
show-words:
	# letters has $(words $(letters)) words: '$(letters)'
```
- the variable "letter" acts as the control variable for the loop
- the loop iterates over the list "a b c d"
- for each item in the list we only expand out the control variable $(letter) as it is written in the assignment of the full variable $(letters)
- as a comment, $(words $(letters)) will return 4
- $(letters) will expand $(letter) in each of the items in the list which will really just resolve to "a b c d" in the end.

This... is a more interesting loop of my own design.

```makefile
loop-func := $(foreach letter,a b c d,\
				$(shell echo $(addprefix hello from ,$(letter))))
```
This function highlights some of the caveats of make's mechanism for expanding
text sections as well as the shell function's operation.

what you would usually expect from this loop is that the body would expand into 4 separte echo calls but what ends up happening is you get the shell function once and the addprefix function 4 times (once per list item). After the shell function finishes the newlines from each echo are colapsed and make resolves one line of output.

If you wanted to separate these... you'd probably have to run them in a target and use $< to use these one by one err something... we'll cross that bridge when we get there is what we'll do.

## Advanced User-Defined Functions
GNU make unfortunately does not provide very many built in functions for debugging makefiles so, to get the task of debugging done you'll have to leverage what has been made available to define functions for your project that make sense for debugging.

### Determining Variable Origins and State
One command that might be of some use for this that is built in to the program is the "origin function" which returns a string that indicates where a variable was defined.
```output
syntax:
        $(origin <var_name>)
```
Here is an example of an assert function that checks if a variable has been defined and returns a message stating that it is undefined if it has not been.

```makefile
define assert
	$(if $1,,$(error Assertion failed: $2))
endef

define assert-defined
	$(call assert,$\
		$(filter-out undefined,$(origin $1)),$\
		'$1' is undefined)
endef
do:
	$(call define-assert,FLARP)
```
This displays the following message in the example makefile for chapter 8 "makefile:59: *** Assertion failed: 'GLARP' is undefined.  Stop."

Before anything executes... after running it a few times I guess, make must be caching this portion of the tree and it will just display the assert failure message before doing anything else, even if you change the variable name.

### Debug (Entering and Leaving) a macro
When debugging a user-defined function you will surely run into situations where you will need to know which macro you are currently running from.

When the call function is determining the order expansion will take for the macro, a variable or function name can be accessed with via the member $0, like in bash.

The following user-defined functions will be useful in your debugging efforts.

```makefile
# $(debug-enter)
debug-enter = $(if $(debug_trace),\
				$(warning Entering $0($(echo-args))))

# $(debug-leave)
debug-leave = $(if $(debug_trace),$(warning Leaving $0))

comma := ,
echo-args   = $(subst ' ','$(comma) ',\
				$(foreach a,1 2 3 4 5 6 7 8 9,'$($a)'))
debug_trace = 1

define a
	$(debug-enter)
	@echo $1 $2 $3
	$(debug-leave)
endef

define b
	$(debug-enter)
	$(call a,$1,$2,hi)
	$(debug-leave)
endef

trace-macro:
	$(call b,5,$(MAKE))
```
This example is kept in the debug_functions and it prints out the function you are entering with the arguments you are entering with and when you exit a function

### The eval Function and metaprogramming
The purpose of the eval function is to parse the text argument given to eval as a makefile, allowing you to carry out operations conditionally and based on arguments given to a macro that gets expanded inside the call to eval. when eval finishes, it collapses it's ouput (possibly/usually nothing) to one line and allows your makefile to continue without errors about missing seperators or
other messages about not being able to use multiline macros/variables in the file.

If you think about what's going on here... eval let's you run code that has been written by code. Say for example you wanted to define a function that not only returns a rule for generating a target but also assigns the variables used by that rule and any macros that would need to be expanded for the rule to have rich and varying functions

The example below is a use case for eval that takes a _\<prefix>_ and a _\<file-list>_ and uses those parameters to define sources,headers and object file lists. Then it returns a rule

```makefile
# $(call program-variables,variable-prefix,file-list)
define program-variables
  $1_sources = $(filter %.c,$2)
  $1_headers = $(filter %.h,$2)
  $1_objects = $(subst .c,.o,$(filter %.c,$2))

  $$($1_objects): $$($1_headers)
endef

ls: $(ls_objects)

$(eval $(call program-variables,ls,ls.c ls.h glob.c glob.h))
```
The order that make runs the makefile in.

1: ```$(eval $(call program-variables,ls,ls.c ls.h glob.c glob.h))
    - Inside to out :)```
   make uses the call function to expand the program-variables macro into this

```makefile
ls_sources = ls.c glob.c
ls_headers = ls.h glob.h
ls_objects = ls.o glob.o

$$(ls_objects): $$(ls_headers)
```
1_A: \$(eval \<all-that-shit-above\>) tells make to process the resolved text as a makefile

1_B: while parsing the text in the sort of make-subshell instance we are in
```
     "$(ls_objects): $(ls_headers)" becomes
```

```makefile
     ls.o glob.o: ls.h glob.h
```
1_C: This is really just short hand for the 2 rules below

```makefile
     ls.o: ls.h
     glob.o: glob.h
```
2: the eval function is literally replaced with the 2 lines above... as if we wrote them in the makefile ourselves

3: the fact that "$(ls_objects)" has been defined and that rules for generating
```
    ls.o and glob.o make it possible to fulfill the target.
```

```makefile
    ls: $(ls_objects)
```
3_A: That line expands out to be

```makefile
     ls: ls.o glob.o
```
4: built-in rules for the ls and, actually before this, the ls.o and glob.o targets are all able to handle the compiling and linking of these products

An important note about the syntax we use in the target definition
"\$\$(\$1_objects): \$\$(\$1_headers)". This needs to be done because of the order expansion happens in.

**First:** Outside/initial instance of make expands the macro via the call function. which is where the variables are actually defined.

**Second:** eval executes the target but it has not actaully inherited the values for those definitions. the eval function is in a subshell and tries to resolve ```$($1_objects) and $($1_headers)``` and comes up with ```$(ls_objects)``` and ```$(ls_headers)```. which INSIDE this instance of make have no meaning so an empty " : " is all that it is able to return.

The double $ tells the make parser in the eval function that the value was defined in the external instance of make.

The other way you could have done this correctly would be by wrapping each of the varaible declarations in an eval function like this.

```makefile
define program-variables
 $(eval $1_sources = $(filter %.c,$2))
 $(eval $1_headers = $(filter %.h,$2))
 $(eval $1_objects = $(subst .c,.o,$(filter %.c,$2)))

  $($1_objects): $($1_headers)
endef

ls: $(ls_objects)

$(eval $(call program-variables,ls,ls.c ls.h glob.c glob.h))
```
Debugging Note: Some helpful tools to remember when trying to debugging functions is the

There you have it buddy... that's real, butt pounding metaprogramming... This is true...

The obvious next place to take this new ability is making templates for different target types we might want to make.

This section of code demonstrates a cool trick you can use to additively use one of these sort of target-generation templates.

```makefile
#$(call program-variables,variable-prefix,file-list)
define program-variables
  $(eval $1_sources = $(filter %.c,$2))
  $(eval $1_headers = $(filter %.h,$2))
  $(eval $1_objects = $(subst .c,.o,$(filter %.c,$2)))

  programs += $1

  $1: $($1_objects)

  $($1_objects): $($1_headers)
endef

# Place all target here, so it is the default goal.
all:

$(eval $(call program-variables,ls,ls.c ls.h glob.c glob.h))
$(eval $(call program-variables,cp,...))
$(eval $(call program-variables,mv,...))
$(eval $(call program-variables,ln,...))
$(eval $(call program-variables,rm,...))

# Place the programs prerequisite here where it is defined.
all: $(programs)
```
Whats happening here is, you want "all" to be the default target but you need the $(programs) variable to be properly defined first (meaning you'd need to complete the 5 eval statements first)

so, we place it at the top and make will see the all rule at the bottom for generating the target and see that it references the $(programs) variable which will be defined at that time because the eval statements will have already parsed their output and set the variable.

The cool part about this is that each time you use the eval statement, you add $1 to the variable because of the statement "programs += $1"
### Function Hooking
In general hooks will normally be a set of functions that are triggered when a piece of code enters a pre-defined state that is associated with that hook so like if you used $(AR) to archive but you also have $(COMSPEC) set in your environment, your collections of hook functions could have a definition for the windows version of the archive command and the linux and the mac one. Portability of your makefile is something you might want to extend using function hooks.

in make we kind of do this by using the the fact that undefined varaibles are reduced to empty strings unless otherwise designed to return an error.

here's an example of what I mean

```makefile
# $(call build-library, object-files)
define build-library
  $(AR) $(ARFLAGS) $@ $1
  $(call build-library-hook,$@)
endef

$(foo_lib): build-library-hook = $(RANLIB) $1
$(foo_lib): $(foo_objects)
        $(call build-library,$^)

$(bar_lib): build-library-hook = $(CHMOD) 444 $1
$(bar_lib): $(bar_objects)
        $(call build-library,$^)
```
The author of this makefile wanted to run "ranlib" after archiving files in the case of the target $(foo_lib) was being archived and "chmod 444" whenever the target is $(bar_lib)

so, it will attempt to execute $(call build-library-hook)
# Commands
As review, here are some facts about commands that you should have in mind for this chapter.

- Commands are essentially one-line shell scripts and make even goes as far as reducing multi-line command recipies for targets into single commands where it can before entering the execution phase

# Metadata
**TAGS:** #Programming #UsefulCommands 