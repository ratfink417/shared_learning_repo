
# Table of Contents

1.  [Front Matter](#org059f5e6)
2.  [Getting Started](#org685047b)
3.  [Using config files](#org18798ea)
    1.  [Dumping Layouts](#org876bb74)
    2.  [Key Bindings](#orgfcae8c5)
    3.  [Useful Screen Commands](#org010e2fc)
4.  [Multi-user Sessions](#org7113630)
    1.  [Enabling multi-user mode](#org39ff496)
    2.  [Basic set up](#org6f0a3d9)
5.  [Useful tricks for screen](#org0779164)
    1.  [Send a command to every terminal](#org6804817)
    2.  [Have each open window ssh to a specific host](#org6ce6366)

GNU screen lets you manipulate your terminal session in ways that will make you look cool
and give you better access and visability into the local/remote sessions you are working with.

For example you can be splitting, tabbing, detaching and reattaching to terminal sessions that
you have running in the background.

This blog post is gonna cover enough of those things to get you started on using the command and
writing scripts and configs that can keep you motivated and productive in the shell(s)


<a href id="org059f5e6"></a>

# Front Matter

First thing to know is some vocabulary that is used in the screen documentation. This article uses the same terms so that you can use this
as a jumping off point to get what you really need from the man pages and the FSF website.

window - a portion of the screen layout capable of containing a terminal
region - a portion of the screen layout that holds multiple windows (split horizontally): vertical splits define new screen regions
session - all the things open and running when you are in an instance of the screen program (layout, open editors and remote connections, etc&#x2026;)
window number - numeral value used to address a window in a screen session
window title - string value used to address a window in a screen session


<a id="org685047b"></a>

# Getting Started

To get a screen session started, just type screen at the shell prompt.

To see where this thing can start to help you out, you should learn these keyboard commands for getting things done in screen.
First thing to keep in mind is that the command key is &ldquo;ctrl-a&rdquo; this tells screen to respond to the next key pressed.

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<tbody>
<tr>
<td class="org-left">split horrizontal</td>
<td class="org-left">ctrl-a S</td>
<td class="org-left">splits the window that is in focus</td>
</tr>


<tr>
<td class="org-left">split vertical</td>
<td class="org-left">ctrl-a</td>
<td class="org-left">splits the window that is in focus</td>
</tr>


<tr>
<td class="org-left">start a session</td>
<td class="org-left">ctrl-a c</td>
<td class="org-left">starts a terminal at the window in focus</td>
</tr>


<tr>
<td class="org-left">select a different window</td>
<td class="org-left">ctrl-a tab</td>
<td class="org-left">&#xa0;</td>
</tr>


<tr>
<td class="org-left">select a window by number</td>
<td class="org-left">ctrl-a &lt;n&gt;</td>
<td class="org-left">where n is the window number</td>
</tr>


<tr>
<td class="org-left">name a window</td>
<td class="org-left">ctrl-a A</td>
<td class="org-left">This prompts you to type in a name for the window in focus</td>
</tr>


<tr>
<td class="org-left">close a window</td>
<td class="org-left">ctrl-a K</td>
<td class="org-left">closes the window that is in focus</td>
</tr>


<tr>
<td class="org-left">detach from a session</td>
<td class="org-left">ctrl-a d</td>
<td class="org-left">this puts your screen session in the background so you can come back to it later</td>
</tr>


<tr>
<td class="org-left">enter command mode</td>
<td class="org-left">ctrl-a :</td>
<td class="org-left">lets you run screen specific commands which will be covered later</td>
</tr>


<tr>
<td class="org-left">command help</td>
<td class="org-left">ctrl-a ?</td>
<td class="org-left">displays all of screen&rsquo;s internal commands and their keyboard short cuts</td>
</tr>


<tr>
<td class="org-left">kill all</td>
<td class="org-left">ctrl-a \</td>
<td class="org-left">kills all of the windows and the scree session you are in</td>
</tr>


<tr>
<td class="org-left">refresh window</td>
<td class="org-left">ctrl-a l</td>
<td class="org-left">in case the data in the window was stale, sends a refresh signal</td>
</tr>


<tr>
<td class="org-left">display window info</td>
<td class="org-left">ctrl-a i</td>
<td class="org-left">shows all of the settings applied to the selected window</td>
</tr>
</tbody>
</table>

Later, I will cover how to remap &ldquo;ctrl-a&rdquo; to whatever you find more suitable and how to set your own key bindings. You&rsquo;ll also learn how to create secondaries to the
&ldquo;ctrl-a&rdquo; keys so that you can map more shortcuts and build your own sort of mnemonic devices into the schema of your keyboard short cuts ie(&ldquo;ctrl-w&rdquo; will be my window commands)

The next important matter is command line options you will need to feel empowered when you use screen.

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<tbody>
<tr>
<td class="org-left">close all windows</td>
<td class="org-left">killall screen</td>
<td class="org-left">this is just a bash command</td>
</tr>


<tr>
<td class="org-left">list all sessions</td>
<td class="org-left">screen -list</td>
<td class="org-left">this bash command lets you see the name of all the sessions you have open so that you can go back to them</td>
</tr>


<tr>
<td class="org-left">re-attach to a session</td>
<td class="org-left">screen sessionname n</td>
<td class="org-left">this bash command lets you pick up a screen session where you left off (still ssh&rsquo;d and screen split/apps open)</td>
</tr>


<tr>
<td class="org-left">preselect a window</td>
<td class="org-left">screen -p n</td>
<td class="org-left">follow -p with a window number or name to preselect it. useful with the -X option for sending commands</td>
</tr>


<tr>
<td class="org-left">send command</td>
<td class="org-left">screen -X command</td>
<td class="org-left">follow -X with the screen command you want to send to a screen session</td>
</tr>


<tr>
<td class="org-left">set default shell</td>
<td class="org-left">screen -s program</td>
<td class="org-left">sets the program that will open when new windows are created in screen. usually your preferred shell</td>
</tr>


<tr>
<td class="org-left">Specify a config file</td>
<td class="org-left">screen -c path</td>
<td class="org-left">starts screen with the configuration file you specify by path</td>
</tr>
</tbody>
</table>


<a id="org18798ea"></a>

# Using config files

The way, we should be using screen is keeping a repository of config files and templates for different types of projects we want to do. This lets us have things
all ready for use when we&rsquo;re doing things like troubleshooting a single site which is different than what would be required for pair programming or deploying a
patch across the entire network.

screen config files are pretty easy to make. It&rsquo;s just a list of all the screen commands that you want the program to execute as soon as it starts.
We haven&rsquo;t covered any actual screen commands but these are executed when you use the shortcuts and you can use them manually by entering the command mode with
&ldquo;ctrl-a :&rdquo; then typing in a command.

To give you what you would need to at least demonstrate this concept I&rsquo;ll list the commands for splitting and opening new windows here.

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<tbody>
<tr>
<td class="org-left">Split a window</td>
<td class="org-left">split</td>
</tr>


<tr>
<td class="org-left">Split a window vertically</td>
<td class="org-left">split -v</td>
</tr>


<tr>
<td class="org-left">Select next window</td>
<td class="org-left">focus</td>
</tr>


<tr>
<td class="org-left">Open a new terminal</td>
<td class="org-left">screen</td>
</tr>
</tbody>
</table>

Below is a screen config file that opens with 3 terminals all logged into a screen session.

    split -v
    focus
    screen
    split
    focus
    screen
    focus
    screen


<a id="org876bb74"></a>

## Dumping Layouts

To build on the last example, it would be preferable in most cases to not have to think about how regions are being split as you type in these commands to come up
with a layout scheme for your config file.

screen has a command that lets you dump the commands needed to produce the exact window layout of your session to a text file that can be used or added to a screen config file.
During a session where you have split your windows just how you want them:

1.  enter command mode &ldquo;ctrl-a :&rdquo;
2.  dump the layout &ldquo;layout dump <path>&rdquo;

where <path> is the path to the file you want to dump the screen commands to.

The contents of this file can be used as a template or added to a config file you already have for just doing the layout portion.
It&rsquo;s output won&rsquo;t contain the &ldquo;screen&rdquo; commands used to open sessions in the windows so you&rsquo;ll need to place those after each time the
&ldquo;focus&rdquo; command is used if screen is what you want run in each of the windows.


<a id="orgfcae8c5"></a>

## Key Bindings

Bindings let you map keystrokes to screen commands in a session. The gist of using it is covered in this section.

command: bind
Synopsis: bind [-c class] key [command [args]]
    key - one or two character sequence
        &rsquo;x&rsquo;    means that &rsquo;x&rsquo; is being bound.
        &rsquo;<sup>x</sup>&rsquo;   means that &rsquo;ctrl-x&rsquo; is being bound.
        &rsquo;\\###&rsquo; means that an ascii number represented in octal is being bound

[-c class]
This options allows you to create and use command binding classes which typically work by you putting
-c <class<sub>name</sub>> before the binding for a bunch of different bindings you want to do for the class
then using the bindkey command to bind a class to a sequence of characters.

This is a nice thing to be able to do because you can organize you key bindings by some common functionality
like window resizing or selection or whatever you wann have command classes for.

EXAMPLE:

    # ctrl-a, space now shows a list of windows
    bind ' ' windows
    
    # ctrl-f now creates a window with a telnet connection to derp.com
    bind ^f screen telnet derp.com
    
    # ctrl-b 0,1,2 now selects window number 10,11,12
    # ctrl-a 0,1,2 also now selects window number 10,11,12
    bind -c demo1 0 select 10
    bind -c demo1 1 select 11
    bind -c demo1 2 select 12
    bindkey "^B" command -c demo1    ctrl-b,0,1 or 2 now selects window number 10,11 or 12
    
    bind -c demo2 0 select 10
    bind -c demo2 1 select 11
    bind -c demo2 2 select 12
    bind - command -c demo2                  ctrl-a,0,1 or 2 now selects window number 10,11 or 12


<a id="org010e2fc"></a>

## Useful Screen Commands

The next step in creating useful config files for screen is to have a list of commands in your pockets for defining them. You&rsquo;ll want these running when screen initializes or
whenever keystrokes you have mapped to them have been triggered.

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<tbody>
<tr>
<td class="org-left">Send a command to all windows in the session</td>
<td class="org-left">at &lt;command&gt;</td>
<td class="org-left">&ldquo;#&rdquo; means the command will be sent to ALL windows but you may put a name with wildcards or a specific number or range of numbers</td>
</tr>


<tr>
<td class="org-left">Send key-strokes to a window</td>
<td class="org-left">stuff &ldquo;keys&rdquo;</td>
<td class="org-left">keys will be replayed on the window &ldquo;<sup>M</sup>&rdquo; is the represents the &ldquo;enter&rdquo; key and is useful for telling a window to run a command</td>
</tr>


<tr>
<td class="org-left">Set the window title</td>
<td class="org-left">title &ldquo;&lt;name&gt;&rdquo;</td>
<td class="org-left">changes the window title to the string &lt;name&gt;</td>
</tr>


<tr>
<td class="org-left">Split the window</td>
<td class="org-left">split [-v]</td>
<td class="org-left">you can split horizontally or vertically</td>
</tr>


<tr>
<td class="org-left">Resize the window</td>
<td class="org-left">resize &lt;direction&gt; &lt;amount&gt;</td>
<td class="org-left">where direction is -h(width),-v(height),-b(both) and amount is +/- a number or percentage %</td>
</tr>


<tr>
<td class="org-left">Select a different window</td>
<td class="org-left">focus &lt;direction&gt;</td>
<td class="org-left">change to another window indicated by the direction   next,prev,up,down,left,right,top,bottom</td>
</tr>


<tr>
<td class="org-left">Name your current session</td>
<td class="org-left">sessionname &ldquo;&lt;name&gt;&rdquo;</td>
<td class="org-left">make sure to pick something that describes what is in the session and why you declared it</td>
</tr>


<tr>
<td class="org-left">Detach from the session</td>
<td class="org-left">detach</td>
<td class="org-left">puts your session in the background, you should probably name your session to something recognizable first</td>
</tr>


<tr>
<td class="org-left">Log the current screen contents</td>
<td class="org-left">hardcopy [-h] &lt;file&gt;</td>
<td class="org-left">dumps the current contents of your screen session to a file. -h tells it to include the scrollback buffer</td>
</tr>


<tr>
<td class="org-left">Enable logging for a window</td>
<td class="org-left">log</td>
<td class="org-left">starts logging input and output for the selected window</td>
</tr>


<tr>
<td class="org-left">Set a logfile path</td>
<td class="org-left">logfile &lt;file&gt;</td>
<td class="org-left">tells the window to log it&rsquo;s input/output to a specific file</td>
</tr>
</tbody>
</table>

These screen command line options are here to cover some things you might want to add to a bash script that runs when your session starts

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<tbody>
<tr>
<td class="org-left">close all windows</td>
<td class="org-left">killall screen</td>
<td class="org-left">this is just a bash command</td>
</tr>


<tr>
<td class="org-left">list all sessions</td>
<td class="org-left">screen -list</td>
<td class="org-left">this bash command lets you see the name of all the sessions you have open so that you can go back to them</td>
</tr>


<tr>
<td class="org-left">re-attach to a session</td>
<td class="org-left">screen sessionname n</td>
<td class="org-left">this bash command lets you pick up a screen session where you left off (still ssh&rsquo;d and screen split/apps open)</td>
</tr>


<tr>
<td class="org-left">preselect a window</td>
<td class="org-left">screen -p n</td>
<td class="org-left">follow -p with a window number or name to preselect it. useful with the -X option for sending commands</td>
</tr>


<tr>
<td class="org-left">send command</td>
<td class="org-left">screen -X command</td>
<td class="org-left">follow -X with the screen command you want to send to a screen session</td>
</tr>


<tr>
<td class="org-left">set default shell</td>
<td class="org-left">screen -s program</td>
<td class="org-left">sets the program that will open when new windows are created in screen. usually your preferred shell</td>
</tr>


<tr>
<td class="org-left">Specify a config file</td>
<td class="org-left">screen -c path</td>
<td class="org-left">starts screen with the configuration file you specify by path</td>
</tr>
</tbody>
</table>


<a id="org7113630"></a>

# Multi-user Sessions

Screen allows you to share open sessions with multiple users. In our case, we&rsquo;re all logged in as root so most of the permissions you might want to set per window or session
but you should at least know how to enable multi-user mode for your session and some of it&rsquo;s mechanics.


<a id="org39ff496"></a>

## Enabling multi-user mode

To enable it, you just need to use the command:
    multiuser on

This allows multiple users to attach to your session
They will have full read and write access to the session if they are root or the same user as you.
Meaning that both of you can control the cursor in that session.

It&rsquo;s useful for collaborative work where you might want to see what another user is doing and
occasionally allow them control.


<a id="org6f0a3d9"></a>

## Basic set up

The easiest thing to do would be to create a config file for a session you want to share.
Make sure to name it something useful for the person you want to share with.
instruct them to run:
    screen sessionname <shared<sub>session</sub>>


<a id="org0779164"></a>

# Useful tricks for screen

These are just some work-arounds for limitations and things I found useful when trying to do things in screen


<a id="org6804817"></a>

## Send a command to every terminal

C-a, :at &ldquo;#&rdquo; stuff &ldquo;ls -l<sup>M</sup>&rdquo;
    you could also, replace the # for a number or name for a specific terminal you would like to address


<a id="org6ce6366"></a>

## Have each open window ssh to a specific host

in a bash script, just place lines that specify which window should send &rsquo;ssh&rsquo; to whichever host it should be remoting to.

    screen -p "0" -X stuff "ssh host0.com^M"
    screen -p "1" -X stuff "ssh host1.com^M"
    screen -p "2" -X stuff "ssh host2.com^M"
    screen -p "3" -X stuff "ssh host3.com^M"
    screen -p "4" -X stuff "ssh host4.com^M"

This will send keystrokes needed to attempt an ssh session with each host on each screen.
If you need to have a user and password, you might try adding a prompt and placing some
variables in the string used by the stuff command.

