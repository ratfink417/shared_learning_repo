# The POSIX Shell
`sh` is the POSIX shell which means it is the shell compliant with the ANSI POSIX/1.1 standard. That's the standard that covers UNIX system design and is a minimum set of what must be included by a system to really be considered UNIX and if you create things that comply with POSIX then you should be pretty certain that it will work in other systems too. So if you write a script that works in `sh` it should work on any of your Linux machines, your over priced Mac book, your NintendoDS that runs netBSD.

This note is mostly some primer and then coverage about special variables and maybe other _hacky looking mojibake/moonspeak-perl bullshit_ that make you think you might have just encountered _Missing.No_ in your vim session. After reading this note, The hope is that you feel a little better about the scripts on your machine that look like this.

![](../Images/Pasted%20image%2020210924160426.png)

You might even end up putting a little of that in your own scripts. Some of it's pretty useful.

# Shell Scripts

## The ShaBang
Usually the first line you'll see in any shell script is _sha-bang_ and it usually looks something like this ```#!/bin/sh``` or ```#!/usr/bin/python``` all this line does is tell your interpreter the path to an executable file that is meant to interpret everything beneath that line till the end of the file _\<EOF\>_ in moonspeak.

## The trap Command
The `trap` command is not something you'll use in an interactive shell section but does show up in a lot of shell scripts.

This command waits for a signal from the OS and performs a given action. You'll see them kinda weaved into portions of the script that do things like create temporary files or set environment variables that could dicker up your session if you had just left them hanging. It's syntax goes like this.
```
trap [action] [signal]
```
The most common one you'll probably see is the **EXIT** signal. This means that the script will run the command the precedes the word **EXIT** whenever it receives that signal. For example, it might look something like this.

```bash
tmpfile=/tmp/tempdata
trap "rm -f $tempfile" EXIT
```
This means that whenever the script completes, it will also remove the /tmp/tempdata file.

Another one you are likely to encounter is **SIGINT** which is the _Interrupt Signal_ usually triggered by a user pressing `ctrl-c` while the script is running.

# Special Variables and Parameters
Usually when writing shell scripts or concocting an inline call to `sh` with the **-c** option is when you'll need to start thinking about ways to handle arguments, stdin/stdout and other shell facilities that you don't really think about as a casual commandline-gamer. When you need access to that sort of stuff is when you want to start thinking about special variables.

## IFS Input Field Separator Variable
This thing shows up in scripts reading input from a file, it's very famous on stackoverflow and other forums when people ask how to read and write to files but need a little more control that redirection can give them. You'll usually see people post solutions that do shit like this.

```bash
while IFS= read -r line; do
    echo "Text read from file: $line"
done < my_filename.txt
```

Essentially, it is a string of special characters which are to be treated as delimiters between words/fields when splitting a line of input. When people are trying to parse each line of text in a file, they'll often do something like the while loop above. 

- set the field separator **IFS** to " " to prevent trimming white space characters like tabs and spaces.
- construct a while loop that reads a line at a time.
- do whatever processing on each line
- direct the output of _my_filename.txt_ to the input of the while loop

You could do the same thing for reading a file or some other input a comma at a time or however you wanted to delimit.

**note:** The IFS variable is also used by the shell to delimit stdin, this includes the things you type into the command line. If the IFS has not been set, the shell will interpret your input to be split by white space _THIS DOESN'T MEAN THE read COMMAND OR ANY OTHER COMMAND WILL DO THE SAME_ and is why you have to set if for the read loop. Also why it doesn't really matter that you unset it, at least for the shell because of it's default action.

## Positional Arguments
Arguments given to a shell script or executable when you type it in on the command line ie:```mv file_one dir_one``` are assigned to variables by the shell without telling you. Each word in that command, separated by a white space _(or whatever the IFS has been set to)_ is seen as a positional argument by your shell and they are assigned to _$0, $1, $2..._ in sequence. So when you run the command above the shell sets 3 temporary variables that fall out of scope when your command finishes executing
- $0 = 'mv'
- $1 = 'file_one'
- $2 = 'dir_one'

It'd be kinda the same way if you used `sh -c`. The shell that you launched would start assigning arguments this way _after the -c flag_ your shell that called it however would set 5 variables though. Obviously the sh, the -c, the mv command and it's arguments would each get a variable.

## The $@ Parameter
The `$@` symbol expands out to all of your positional argument **Starting with $1** so, all of the actual arguments to the initial command and not the command itself.

## The $* Parameter
The `$*` symbol expands to "**\$1**_c_ **\$2**_c..._", where _c_ is the first character of the value of the **IFS** variable. 
- If **IFS** is _unset_, the parameters are separated by spaces. 
- If **IFS** is _null_, the parameters are joined without intervening separators. 

## Less Complicated Special Parameters
<table style="border-collapse:collapse;border-spacing:0" class="tg"><thead><tr><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Symbol</th><th style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Description</th></tr></thead><tbody><tr><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$#</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal"><span style="font-weight:400;font-style:normal">The number of positional parameters</span></td></tr><tr><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$?</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">The exit status of the most recently executed foreground pipeline</td></tr><tr><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$-</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal"><span style="font-weight:400;font-style:normal">The current option flags as specified upon invocation, by the </span><span style="font-weight:bold">set </span>builtin command, or those set by the shell itself (such as the <span style="font-weight:bold">-i </span>option)</td></tr><tr><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$$</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal"><span style="font-weight:400;font-style:normal">The process ID of the shell. In a () subshell, it expands to the process ID of the current shell, not the subshell</span></td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">$!</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal"><span style="font-weight:400;font-style:normal">The process ID of the most recently executed background (asynchronous) command</span></td></tr></tbody></table>

**note:** The description for the _\$-_ parameter is just talking about any of the command flags that your shell runs with. So if you just use the `sh -c` command, you'd surely see the -c as part of the expansion of this variable.

## Parameter Expansion
Besides just having special parameter values, you may also expand parameters out for more flexibility with the parameter expansion characters. These will take a form like this.

```${something}```

### Basic Expansion
The most basic options will have this syntax.
``` ${[parameter]:[-=?+][number : number]}```
where parameter is a variable or parameter and each of the operators transform the parameter in some way. The trailing number:number represent an offset and size for a substring in the parameter.

**${parameter:-word}**
If **parameter** is _unset_ or _null_, the expansion of **word** is substituted. Otherwise, the value of **parameter** is substituted.

**${parameter:=word}**
If **parameter** is _unset_ or _null_, the expansion of **word** is assigned to **parameter**. The value of **parameter** is then substituted. 
- **note:** Positional parameters and special parameters may not be assigned to in this way.

**${parameter:?word}**
If **parameter** is _null_ or _unset_, the expansion of **word** (or a message to that effect if word is not present) is written to the standard error and the shell, if it is not interactive, exits. Otherwise, the value of **parameter** is substituted.

**${parameter:+word}**
If **parameter** is _null_ or _unset_, **nothing** is substituted, otherwise the expansion of **word** is substituted.

**${parameter:offset}**
```output
$ string=01234567890abcdefgh
$ echo ${string:7}
7890abcdefgh
```
**${parameter:offset:length}**
```output
$ string=01234567890abcdefgh
$ echo ${string:7:2}
78
```
### Other Expansions
#### ${!prefix@} and ${!prefix*}
Expands to the **names of variables whose names begin with prefix**, separated by the first character of the `IFS` special variable. 

When ‘@’ is used and the expansion appears within double quotes, each variable name expands to a separate word.

#### ${!name[@]} and ${!name[*]}
If name **is** an array variable: expands to the list of array indices (keys) assigned in name. 

If name **is not an array**: expands to 0 if **name** is **set and null** otherwise. When ‘@’ is used and the expansion appears within double quotes, each key expands to a separate word.

#### ${#parameter}
The **length in characters of the expanded value of parameter** is substituted. 
If **parameter** is ‘\*’ or ‘@’: the value substituted is the **number of positional parameters.** 

If **parameter is an array name subscripted by ‘\*’ or ‘@’:** the value substituted is the **number of elements in the array.** 

If **parameter is an indexed array name subscripted by a negative number**: that number is interpreted as relative to one greater than the maximum index of parameter
- so negative indices count back from the end of the array, and an index of -1 references the last element.

#### ${parameter#word} and ${parameter##word}
The **word** is expanded to produce a pattern and matched according to the pattern matching rules from [[regex]] 

If the pattern matches the beginning of the expanded value of parameter, then the result of the expansion is the expanded value of parameter with the shortest matching pattern (the ‘#’ case) or the longest matching pattern (the ‘##’ case) deleted. 

If parameter is ‘@’ or ‘\*’, the pattern removal operation is applied to each positional parameter in turn, and the expansion is the resultant list. If parameter is an array variable subscripted with ‘@’ or ‘\*’, the pattern removal operation is applied to each member of the array in turn, and the expansion is the resultant list.

#### ${parameter\%word} and ${parameter\%\%word}
The word is expanded to produce a pattern and matched according to the pattern matching rules from [[regex]]. 

If the pattern matches a trailing portion of the expanded value of parameter, then the result of the expansion is the value of parameter with the shortest matching pattern (the ‘%’ case) or the longest matching pattern (the ‘%%’ case) deleted. 

If parameter is ‘@’ or ‘\*’, the pattern removal operation is applied to each positional parameter in turn, and the expansion is the resultant list. 

If parameter is an array variable subscripted with ‘@’ or ‘\*’, the pattern removal operation is applied to each member of the array in turn, and the expansion is the resultant list.

# Redirection
You definitely already know this but the shell lets you pipe stuff from stdout of one command to stdin of another and write/append output to the end of files but before moving on, the most basic examples are.

- `$ ls > file.txt` writes over or creates file.txt with output of ls command
- `$ ls >> file.txt` _append_ output to file.txt
- `$ read SOME_VAR < file.txt` uses file.txt as input to the read command and sets _$SOME_VAR_ to whatever it read
- `$ ls -derp 2> /dev/null` suppress error output by sending it to /dev/null

And the file descriptors made available to you by the shell
- 2: error content presented to stderr
- &1: the stdout output

With those out of the way, you should be able to make use or sense out of some of these weird patterns you'll find in shell scripts

## Taking Multi-Line User Input
You can use the append redirection operator and a _terminating sequence_ to redirect stdin to a command. A lot of people who have this in their code or tell you to use it will use `EOF` as the terminating sequence because it stands for End Of File but it can really be anything. Take a look

```output
$ wc -c << test
heredoc> asdf
heredoc> asdf
heredoc> test
10
```
The facility at play here is called the _heredoc_ which is a temporary file created by the shell that gets redirected as input once the terminating sequence is met.

**note:** The count of 10 characters makes sense because _\\n newline_ is counted.

You should totally experiment with this. It lets you even follow the heredoc with another redirection or pipe. You'll be able to take multi-line input and idunno transform it with sed or grep things from it and send it out to other files or devices if you want.

```output
$ wc -c << test > file.txt

heredoc> asdf
heredoc> asdf
heredoc> test
```
The heredoc and redirection is a part of what's so cool about everything being a file in UNIX.
## The here-string
Just like the temporary heredoc, a buffer can be made for a string meant to be redirected as input to a command with the `<<<` operator.

A brief example
```output
derpvar= "hello.txt"
cat <<< $derpvar
```

## Redirect to another terminal
While I'm not totally sure why you'd need to do this, _everything is a file_ lets you send your output from a command in one terminal to another terminal.

First figure out which tty device file belongs to your one of the terminals with the `tty` command.

then run a command on another terminal with a redirection to that terminal device. like this

```output
$ echo "hello" > /dev/pts/1
```

maybe you are running a command that gives continuous output like `tail -f` or `tcpdump` and you'd rather background it on this terminal and show the output on another one.

or maybe another person is on the system at the same time as you and you wanna share some output with them.

You'd just figure out which tty they had with the `w` command and send output to that terminal. Provided you were root or had access to it.

## tee Command Redirection
The `tee` command lets you send a copy of your input and output together to another file. Something fun you might do with this is share a copy of your ssh session with someone who's got a terminal you can write to.

I did one with myself like this

```
$ ssh plex@plex | tee /dev/pts/1
```

## Named Pipe
Named pipes are useful structures that you'll see some scripts and commands take advantage of by calling the command `mkfifo`. This creates a file that acts as a buffer between two running commands.

if you just created one and tried to cat it, you'd see a blank screen until some other command/person/device/whatever wrote to it and you would instantly have it sent to your stdout. 

![](../Images/Pasted%20image%2020210925172053.png)


# Process Substitution
Piping the stdout of a command into the stdin of another is a powerful technique. But, what if you need to pipe the stdout of _multiple_ commands? This is where _process substitution_ comes in.

_Process substitution_ feeds the output of a process (or processes) into the stdin of another process.

Process substitution syntax looks like this ```COMMAND <(substitution 1) <(substitution 2)...```

you can direct the substitution as input or output.
Input:  **>(command_list)**
Output: **<(command_list)**

This is cool for when you want the output of multiple command to stand in as arguments for another command.

As an example, if you wanted to diff the contents of two different directories.

```output
diff <(ls test) <(ls test2)
0a1
> file_four
```
You wouldn't be able to use sub-shells in the standard way because the output of the first would be interpreted as multiple lines of input.

This works because `diff` is designed to take files as an input which is exactly what gets created by process substitution.

# Crazy if statements
The inline kind of if statements that just sit between two square brackets.

## Variable conditions
### if string is empty, then..
```
if [ -z $str ]	
```

### if string is Not empty, then..
```
if [ -n $str ]	
```

### if two numbers are equal, then..
```
if [ $num1 -eq $num2 ]	
```

### if two numbers are not equal, then..
```
if [ $num1 -ne $num2 ]	
```

### if num1 is lesser than num2, then..
```
if [ $num1 -lt $num2 ]	
```

### if num1 is lesser than OR equal to num2, then..
```
if [ $num1 -le $num2 ]	
```

### if num1 is greater than num2, then..
```
if [ $num1 -gt $num2 ]	
```

### if num1 is greater than OR equal to num2, then..
```
if [ $num1 -ge $num2 ]	
```

### If str1 matches the regex then..
```
The regex used in the below example checks if $str is a valid email address.

if [[ $str =~ ^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,4}$ ]]	
```
- **note:** the double square brackets is used here for reqular or conditional expressions.

### if $num1 is lesser than $num2, then..
```
if (( $num1 < $num2 ))	        
if (( $num1 < $num2 ))	        
```
- **note** that double round brackets is used for arithmatic operation.

### noclobber option prevents overwriting existing files with the > operator. 
```
if [ -o noclobber ]	If OPTIONNAME is enabled, then.. 
```

### If the condition (name is vijay) is not true
```
if [ ! name == "vijay" ]	
```

### If both conditions are true, then..
```
if [ name == "vijay" ] && [ age -gt 18 ]	
```

### if one of the conditions is true, then..
```
if [ name == "vijay" ] || [ age -gt 18 ]
```

## File Conditions
### if file Exists..
```
[ -e file.txt ]	
```
### if file is readable
```
[ -r file.txt ]	
```
### if the file is a Symlink
```
[ -h file.txt ]	

```
### if the file name specified is a Directory
```
[ -d files ]	

```
### if file is Writable
```
[ -w file.txt ]	

```
### if file Size is > 0 bytes
```
[ -s file.txt ]	

```
### if the file name specifued is actually a file and not a directory
```
[ -f file.txt ]	

```
### if file is Executable
```
[ -x file.txt ]	

```
### if file1.txt is newer than file2.txt
```
[ file1.txt -nt file2.txt ]	

```
### if file1.txt is older than file2.txt
```
[ file1.txt -ot file2.txt ]	

```
### if file1.txt and file2.txt are the same
```
[ file1.txt -ef file2.txt ]
```
# Metadata
**TAGS:** #Linux #Reference #UsefulCommands #Recipies 