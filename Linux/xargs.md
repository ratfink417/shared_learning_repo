# xargs
The **xargs** utility takes standard input from a pipe or redirect and outputs a command with the xargs input set up however you defined them. To illustrate this, think about if you had a file with a list of directories in it that you wanted to send to the `ls` command. You cant just do something like `cat file.txt | ls` because `ls` won't accept arguments from stdin that way. What you might do is cat out that file in the opening of a for loop but xargs keeps us from having to do that.So, _Something like_ ``` cat file.txt | xargs ls ``` will feed the shell something like ```ls line1 line2 line3 line4``` and that's kind of the whole point of this command.

# Show What Commands xargs Is Actually Feeding The Shell
Actually you can confirm that my last example is doing exactly what was expected by using the **-t** option with xargs.

```
$ cat file.txt xargs -t ls
ls chapter1 chapter10 chapter11 chapter12 chapter13 chapter2 chapter3 chapter4 chapter5 chapter6 chapter7 chapter8 chapter9 inc file.txt

chapter1:
compilation_example.c  hello.exe  Makefile

chapter10:

chapter11:
dataleak-test.c      date.c            dta-execve.cpp  execve-test.c           execve-test-overflow-implicit.c
dataleak-test-xor.c  dta-dataleak.cpp  echo.c          execve-test-overflow.c  Makefile

...
```
# Building Commands From xargs
The **-I** flag lets you specify a place holder (the [man page](Using%20System%20Documentation.md) actually refers to it as a _replace string_) that you can use as part of a larger command that you're trying to build from xargs.

Say you wanted to add the _.mkv_ file extension to the end of every file in a directory.

```
ls | xargs -I {} mv {} {}.mkv
```

**note:** you can use _almost any_ replacement string you like. I used double curly braces here but you can put just about anything that isn't another command.

# Adjusting Number Of Arguments
You can use the **-n** flag followed by an integer to tell xargs how many arguments at a time to grab. This means xargs will read what you have piped into it, grab _X_ lines at a time and run a command with those as arguments in order. For example.

```output
$ seq 10 | xargs -t -n2 echo
echo 1 2
1 2
echo 3 4
3 4
echo 5 6
5 6
echo 7 8
7 8
echo 9 10
9 10
```
see, it's grabbing 2 items at a time and feeding them as arguments to a command. If you have an odd number of arguments, xargs still tries to run with the extra odd ends as arguments. I think you can be explicit in telling it not to with the **-r** flag but... it's a GNU specific extension to xargs and if you put that in your script it won't work on every UNIX-like system or derivatives like the BSD's so not gonna work on your Mac.

**note:** If you try to use the _-n_ flag with _-I (string replacement)_, xargs will still try to run but will let you know that _-I_ and _-n_ are mutually exclusive and this makes sense. xargs would have know way of reading your mind and knowing which arg should be replaced and there's no reason to think you meant all of them because it'd just be real weird to use xargs for putting the same argument in there a bunch of times using the replacement string function.

# Invoking Sub-Shells On An Argument With xargs
Best way to get this to happen is the the shell command `sh` with the **-c** option. When you do this from xargs, a sub-shell for batch of arguments parsed out from the stdin of xargs will be spawned with the command and arguments that you gave the `sh -c` command.

It might look something like this when you do it.

```output
$ ls | xargs -t -I {} sh -c 'cat {} | grep baz'
sh -c 'cat file_four | grep baz'
sh -c 'cat file_one | grep baz'
sh -c 'cat file_three | grep baz'
baz
baz
sh -c 'cat file_two | grep baz'
```
just cats and greps each item from `ls` output. kinda contrived, a little bit redundant but... show that you can pass all that stuff in as a string replacement and push each output separately through more pipes in individual sub-shells.

you could have just as easily handled all arguments in a single subshell like this

```output
ls | xargs -t sh -c 'cat "$@" | grep baz'
sh -c 'cat "$@" | grep baz' file_four file_one file_three file_two
baz
baz
```

The **$@** is a [special parameter](sh.md#Special%20Variables%20and%20Parameters) to the `sh` shell that just means _all of the arguments passed to sh_. Which is why the output was the way it was.
`sh -c 'cat "$@" | grep baz' file_four file_one file_three file_two`

# Metadata
**TAGS:** #Linux #Reference #UsefulCommands #Recipies 