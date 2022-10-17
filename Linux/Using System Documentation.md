# Using Documentation
The man pages are _usually_ the best documentation you're gonna find for something provided on a \*nix system and in most cases they'll be available right at the terminal where you sit without having to google shit or dig up some outdated pdf or something like that. Exceptions of course being things like appliances and embedded systems where the vendor locked you out because of the game theory rules of capitalism that shitted up things like this for us or they simply cannot include them because of the technical limitations.

Anyway the path to your goals with a \*nix system will be much more clear when you know how to use this stuff.

## How Man Pages are Organized
Man pages _(if supplied)_ will be divided into 9 sections. While not every section might apply to a tool and might not be present, you'll know by looking at the title of the man page which section it's in and you'll be able to form your searches for man pages based on this criteria.

<table style="border-collapse:collapse;border-spacing:0" class="tg"><thead><tr><th style="border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal"></th><th style="border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Sections of the man pages</th></tr></thead><tbody><tr><td style="border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">1</td><td style="border-color:inherit;border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Executable programs or shell command</td></tr><tr><td style="border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">2</td><td style="border-color:inherit;border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">System calls (C functions provided by the kernel)</td></tr><tr><td style="border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">3</td><td style="border-color:inherit;border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Library calls (functions within programs and libraries)</td></tr><tr><td style="border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">4</td><td style="border-color:inherit;border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Special files (usually found in /dev)</td></tr><tr><td style="border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">5</td><td style="border-color:inherit;border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">File formats and conventions ( config files and that kinda stuff)</td></tr><tr><td style="border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">6</td><td style="border-color:inherit;border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Gayyyymez </td></tr><tr><td style="border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">7</td><td style="border-color:inherit;border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Misc (including macro packages and conventions)</td></tr><tr><td style="border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">8</td><td style="border-color:inherit;border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">System administration commands (usually only for root)</td></tr><tr><td style="border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">9</td><td style="border-style:solid;border-width:0px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">Kernel routines</td></tr></tbody></table>

## Searching For a Man Page
There are only a few techniques you're likely to use when looking for the man page you need. 

You might just guess what you need based on the thing your trying to figure out.
- **For Example**
	you're trying to find how you can add the setting you need in the _sudoers_ configuration file. You know that's the right file and _now_ you know which section of man will cover file formats and config files so just run ``` man 5 sudoers ```
	

You might be trying to figure out which configuration file is used to get some kind of behavior from _sshd_.
- **Here is where I might**
Run ```man sshd``` and check the references at the bottom of the page. Most people writing these pages leave references at the bottom that have to do with the tool that you're looking up

```manpage
     /run/sshd.pid
             Contains the process ID of the sshd listening for connections (if there are several daemons running concurrently for different ports, this contains
             the process ID of the one started last).  The content of this file is not sensitive; it can be world-readable.

SEE ALSO
     scp(1), sftp(1), ssh(1), ssh-add(1), ssh-agent(1), ssh-keygen(1), ssh-keyscan(1), chroot(2), login.conf(5), moduli(5), sshd_config(5), inetd(8),
     sftp-server(8)

AUTHORS
     OpenSSH is a derivative of the original and free ssh 1.2.12 release by Tatu Ylonen.  Aaron Campbell, Bob Beck, Markus Friedl, Niels Provos, Theo de Raadt and
     Dug Song removed many bugs, re-added newer features and created OpenSSH.  Markus Friedl contributed the support for SSH protocol versions 1.5 and 2.0.  Niels
```
that _SEE ALSO_ portion will usually have the next few feet of the rabbit hole you're trying to go down.

Maybe you're so lost that you don't even know where to look. At this point, as a last ditch before getting abused on stack overflow is, you can search using ``` man -k <regex or keyword> ``` another thing you might consider using is ``` man -f <some command> ``` to see which pages are reference in the man page for _\<some command\>_



## Reading The Man Page
Once you've found the man you're looking for the content might be a little overwhelming at first. Especially for a page that covers a lot of command flags or key words. Take a look at the man page for the _ip_ command.

This is just the top part of the page.
```manpage
NAME
       ip - show / manipulate routing, network devices, interfaces and tunnels

SYNOPSIS
       ip [ OPTIONS ] OBJECT { COMMAND | help }

       ip [ -force ] -batch filename

       OBJECT := { link | address | addrlabel | route | rule | neigh | ntable | tunnel | tuntap | maddress | mroute | mrule | monitor | xfrm | netns | l2tp | tcp_metrics
               | token | macsec | vrf | mptcp }

       OPTIONS := { -V[ersion] | -h[uman-readable] | -s[tatistics] | -d[etails] | -r[esolve] | -iec | -f[amily] { inet | inet6 | link } | -4 | -6 | -I | -D | -B | -0 |
               -l[oops] { maximum-addr-flush-attempts } | -o[neline] | -rc[vbuf] [size] | -t[imestamp] | -ts[hort] | -n[etns] name | -N[umeric] | -a[ll] | -c[olor] |
               -br[ief] | -j[son] | -p[retty] }

OPTIONS
       -V, -Version
              Print the version of the ip utility and exit.

       -h, -human, -human-readable
              output statistics with human readable values followed by suffix.
```
Holy fuck right? Well let's unpack some of this and if you wanna follow along, keep the man page for _ip_ open in another window as we step through some of this.

**NAME** is the first section you'll see and it just give you the name of the thing that the man page is about and a brief description

**SYNOPSIS** Shows you different calling conventions for the command. In the case of this man page, some extra annotations were made.

The rules here are anything between \[\] symbols is optional. Anything in the curly braces {} is part of a set. In this case, ```{COMMAND | help}``` means the last string in your on the cli when you call this may either be _COMMAND_ **OR** _help_ where _COMMAND_ is a command you'll find covered in the documentation and _help_ is the literal string "help".

First there are two ways you might call the command from the command line. 
```
ip [OPTIONS] OBJECT {COMMAND | help}
ip [-force] -batch filename
```
That bottom one is short and sweet so we'll pull on that thread first. The **OPTIONS** section tells you what ```-force``` and ```-batch <FILENAME>``` do already. _-batch_ lets you let's you feed input to the _ip_ **COMMAND** in series either from a file or a pipe and _-force_ will run each **COMMAND** in the series you give it despite any failures that might occur.

The top one ```ip [OPTIONS] OBJECT {COMMAND | help}``` is a little more dense. Since the **OPTIONS** section explains itself pretty nicely, we want to know what the hell could be meant by _OBJECT_

I'd invite you now to scroll down to the section **IP - COMMAND SYNTAX** alternatively, you could have searched for the string _OBJECT_ but here is that portion of the page.

```manpage
IP - COMMAND SYNTAX
   OBJECT
       address
              - protocol (IP or IPv6) address on a device.

       addrlabel
              - label configuration for protocol address selection.

       l2tp   - tunnel ethernet over IP (L2TPv3).

       link   - network device.

       maddress
              - multicast address.
```
I've truncated the output here for brevity but as you can see the values you can put for object vary in a way that you need a vague description to cover what can go here. It's not cuz the man page writer was a dick, it's just the most appropriate word to use. So now we can look at the syntax as something more like 
```
ip [OPTIONS] <THING-YOU-WILL-DO-SOMETHING-TO> <A-COMMAND OR help>
```
and we now know what those objects can be. An even better thing to pay attention to here is the **SEE ALSO** section at the bottom of the page because it can explain these objects in more precise detail. Take a look, these were you're objects listed at the top.
```manpage
       OBJECT := { link | address | addrlabel | route | rule | neigh | ntable | tunnel | tuntap | maddress | mroute | mrule | monitor | xfrm | netns | l2tp | tcp_metrics
               | token | macsec | vrf | mptcp }

```
and these are the pages referenced in **SEE ALSO**. I'm sure you've made the connection here.
```manpage
SEE ALSO
       ip-address(8), ip-addrlabel(8), ip-l2tp(8), ip-link(8), ip-maddress(8), ip-monitor(8), ip-mptcp(8), ip-mroute(8), ip-neighbour(8), ip-netns(8), ip-ntable(8), ip-
       route(8), ip-rule(8), ip-tcp_metrics(8), ip-token(8), ip-tunnel(8), ip-vrf(8), ip-xfrm(8)
       IP Command reference ip-cref.ps
```

Now for the commands, the man page gives us this short passage.
```manpage
   COMMAND
       Specifies the action to perform on the object.  The set of possible actions depends on the object type.  As a rule, it is possible to add, delete and show (or
       list ) objects, but some objects do not allow all of these operations or have some additional commands. The help command is available for all objects. It prints
       out a list of available commands and argument syntax conventions.

       If no command is given, some default command is assumed.  Usually it is list or, if the objects of this class cannot be listed, help.

```
Just having dissected what the objects are and how to learn more about them, this small body of text all of the sudden has so much more substance. From here you can either investigate the available **COMMANDS** for an **OBJECT** by using a command like ``` ip link help``` which will give you a short listing of the commands or you can take a closer, more thorough look at ``` man ip-link```

# Third Party Documentation
When the guidance you seek is not part of your system but from a third party application, you still have a lot of options that will serve you better than seeking advice from strangers you are hoping are sympathetic to you cause.

## Check The Documentation Folder
Most applications of sufficient complexity will ship with documentation that you can find in your file system. Usually this will be kept in a folder in ```/usr/shar/doc/<application-name>``` or in some rare cases, I've found it in ```/var/lib/<application-name>/<something>``` sometimes this documentation will be in html which is a little bit of a bummer but you can just view it through a program like _elinks_ from the cli or your web browser.

If it's not there, you can sometimes score some documentation for the application by searching for it with your package manager. Search using the app's name as the keyword and just look for packages with ```-doc``` or something like that in the name and you might get what you need.

## Code Documentation
It comes down to this sometimes but when it does sometimes you get some very useful insights into the thing you're trying to use. The trick here is to check the application's folder which is often ```/var/lib/<name>``` or sometimes ```/opt/<name>``` maybe ```/usr/src/<name>``` or ```/usr/include/<name>``` there are many more you can find them in and these directories are typically for different artifacts from the installation of the program like include files, the directory the app runs from or anything else really.

The point is, you do your diligence in finding one of these path's relevant to what it is you need to find until you've reached the source code. Sometimes the code is clear and almost documents itself as you read it. Other times you'll find useful comments in here and once in a while, the best case scinario, the comments will be formatted as document strings. Meaning they can be parsed by a tool that makes code documentation more readable to the lost souls trying to understand it.

An example where this came in handy for my was when I was trying to learn more about the _network_ filter supplied by ansible. This thing wasn't covered even in the online documentation sources for the ansible project. Turned out that the python code that implemented the filter used by ansible could be found in ```/var/lib/python/site-packages/ansible/filters/``` and the comments were formatted for use by the _pydoc_ documentation viewer tool from the command line. The output was really easy on the eyes.

# Metadata
**TAGS:** #Linux #Reference 