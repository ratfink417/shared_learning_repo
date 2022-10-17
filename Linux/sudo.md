# Introduction
As defined in the sudo [[Using System Documentation|man]] page:
> _**sudo** allows a permitted user to execute a command as the superuser
> or another user, as specified by the security policy.  The invoking
> user's real (**not** effective) user-ID is used to determine the user
> name with which to query the security policy. _
     
The `sudo` command stands for "*Super User Do*" This is a critical application in linux. 

# Flags
`sudo -l` -  Lists all the permissions the currently logged in user can run as sudo


# sudoers file
Located  at `\etc\sudoers`, this Main file determines all sudo permissions by default: 


## [Including other files from within sudoers](https://www.sudo.ws/docs/man/1.8.15/sudoers.man/#Including_other_files_from_within_sudoers)

It is possible to include other _sudoers_ files from within the _sudoers_ file currently being parsed using the `#include` and `#includedir` directives.

This can be used, for example, to keep a site-wide _sudoers_ file in addition to a local, per-machine file. For the sake of this example the site-wide _sudoers_ will be /etc/sudoers and the per-machine one will be `/etc/sudoers.local`. To include `/etc/sudoers.local` from within `/etc/sudoers` we would use the following line in `/etc/sudoers`:

```
#include /etc/sudoers.local
```

When `sudo` reaches this line it will suspend processing of the current file (/etc/sudoers) and switch to /etc/sudoers.local. Upon reaching the end of /etc/sudoers.local, the rest of /etc/sudoers will be processed. Files that are included may themselves include other files. A hard limit of 128 nested include files is enforced to prevent include file loops.

If the path to the include file is not fully-qualified (does not begin with a ‘`/`’, it must be located in the same directory as the sudoers file it was included from. For example, if /etc/sudoers contains the line:

```
#include sudoers.local
```

the file that will be included is /etc/sudoers.local.

The file name may also include the `%h` escape, signifying the short form of the host name. In other words, if the machine's host name is “xerxes”, then

```
#include /etc/sudoers.%h
```

will cause `sudo` to include the file /etc/sudoers.xerxes.

The `#includedir` directive can be used to create a sudoers.d directory that the system package manager can drop _sudoers_ rules into as part of package installation. For example, given:

```
#includedir /etc/sudoers.d
```

`sudo` will read each file in /etc/sudoers.d, skipping file names that end in ‘`~`’ or contain a ‘`.`’ character to avoid causing problems with package manager or editor temporary/backup files. Files are parsed in sorted lexical order. That is, `/etc/sudoers.d/01_`first will be parsed before `/etc/sudoers.d/10_second`. Be aware that because the sorting is lexical, not numeric, `/etc/sudoers.d/1_whoops` would be loaded _after_ `/etc/sudoers.d/10_second`. Using a consistent number of leading zeroes in the file names can be used to avoid such problems.

Note that unlike files included via `#include`, `visudo` will not edit the files in a `#includedir` directory unless one of them contains a syntax error. It is still possible to run `visudo` with the `-f` flag to edit the files directly, but this will not catch the redefinition of an _alias_ that is also present in a different file.

# Wildcards
`sudo` allows shell-style _wildcards_ (aka meta or glob characters) to be used in host names, path names and command line arguments in the _sudoers_ file. Wildcard matching is done via the glob(3) and fnmatch(3) functions as specified by IEEE Std 1003.1 (“POSIX.1”).

`*`  - Matches any set of zero or more characters (including white space).

`?` - Matches any single character (including white space).

`[...]` - Matches any character in the specified range.

`[!...]` - Matches any character _not_ in the specified range.

`\x` - For any character ‘x’, evaluates to ‘x’. This is used to escape special characters such as: ‘`*`’, ‘`?`’, ‘`[`’, and ‘`]`’.

Note that these are not regular expressions.

Unlike a regular expression there is no way to match one or more characters within a range.

Character classes may be used if your system's glob(3) and fnmatch(3) functions support them. However, because the ‘`:`’ character has special meaning in _sudoers_, it must be escaped. For example:
```
/bin/ls [[\:alpha\:]]*
```

Would match any file name beginning with a letter.

Note that a forward slash (‘`/`’) will _not_ be matched by wildcards used in the file name portion of the command. This is to make a path like:

```
/usr/bin/*
```

matches `/usr/bin/who` but not `/usr/bin/X11/xterm`.

When matching the command line arguments, however, a slash _does_ get matched by wildcards since command line arguments may contain arbitrary strings and not just path names.

Wildcards in command line arguments should be used with care.

  
Command line arguments are matched as a single, concatenated string. This mean a wildcard character such as ‘`?`’ or ‘`*`’ will match across word boundaries, which may be unexpected. For example, while a sudoers entry like:
```
%operator ALL = /bin/cat /var/log/messages*
```
will allow command like:

```
$ sudo cat /var/log/messages.1
```

It will also allow:

```
$ sudo cat /var/log/messages /etc/shadow
```

which is probably not what was intended. In most cases it is better to do command line processing outside of _sudoers_ in a scripting language.

## Exceptions to wildcard rules

The following exceptions apply to the above rules:

**`""`**

If the empty string `""` is the only command line argument in the _sudoers_ entry it means that command is not allowed to be run with _any_ arguments.

**sudoedit**

Command line arguments to the _sudoedit_ built-in command should always be path names, so a forward slash (‘`/`’) will not be matched by a wildcard.
The `sudoers` file does not utilize regex; however, there are some useful characters to help define scope and wild cards

