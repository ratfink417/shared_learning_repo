# Netcat
**Netcat** or **nc** is a utility tool that uses TCP and UDP connections to read and write to/from a network connection. It has a lot of options and handles IO nicely which makes it very scriptable so you can use it in all kinds of applications.

## Limitations
netcat is not a multithreaded program and because of this, it won't be capable of spawning multiple processes for speaking or listening on connections on it's own at least. So if you need multiple connections to a host, netcat isn't really the tool for that job.

# Getting Started with Netcat
Basic netcat commands follow this syntax ```nc [Target IP Address] [Target Port]``` for example if you wanted to send a **GET** request to _google.com_ to get the html of it's landing page, you could construct the request ```GET / HTTP/1.0\r\n\r\n``` and pipe that from ```echo``` to ```nc``` like this ```$ echo GET / HTTP/1.0\r\n\r\n | nc google.com 80``` now that text is being sent into netcat which will redirect it over to _google.com_ on port _80_ and the output to your screen will be the html content of the google landing page.

## Listening Mode
In listening mode, netcat will wait for a connection instead of sending data out over one. This is useful when you want to set up access to a resource available to the machine you run netcat from and make it available somewhere else on the network.

The **-l** option runs netcat in listen mode you can further specify an address and port to listen for with the **-s** and **-p** flags. A typical listen command will look something like this.

```
nc -l [-s REMOTE ADDRESS] [-p LOCAL PORT] [| or > SOME FILE OR COMMAND]
```

For example if you wanted to just listen for plain text sent over port 4444 on your machine you could first run this command.
```
nc -l 4444 >&1
```
listen to port 4444 and direct it's output to the stdout of the terminal you are in.

then on a remote machine run something like this to get "Hello netcat " printed in the terminal
```
echo "Hello netcat" | nc <REMOTE IP> 4444
```
# Netcat Recipies
The following are just cool hackey things I've found you can do with netcat as I have been using it.

## Connect Through a Proxy Server
## Conduct a Port Scan
netcats output in standard mode goes to stderr so, to grep through it's findings you'll need to merge stderr to the stdout stream before you can pipe that data to a grep command.

The **-z** flag causes netcat to send _zero_ data ie an empty packet to the address and port that you give it and the **-v** _verbose_ flag sends the send and receive data to stderr. 

```
nc -vz 69.69.69.69 1-1000 2>&1 | grep succeeded
```

So if you call `nc -vz` and give it an IP and port range as arguments, it will send empty packets out to that IP over a range of ports and report what it had gotten back from the IP. 
## Create a Reverse Shell

- create a named pipe
- send the content of the named pipe to the input of an `sh` session
- [Redirect the stderr and stdout](sh.md#Redirection) of an interactive `sh` session to the netcat listener of the localhost _127.0.0.1_ on a chosen port
- send the output of netcat back into the same named pipe that's feeding the `sh` session.

**On server side:**
```
$ rm -f /tmp/f; mkfifo /tmp/f
$ tail -f /tmp/f | /bin/sh -i 2>&1 | nc -l 127.0.0.1 1234 > /tmp/f
```
So once connection, when you send `ls` to netcat on the client side. 
- the string ls goes to the named pipe _/tmp/f_ on the remote host.
- /bin/sh calls `ls` and sends stdout and stderr to it's netcat command which sends it back into the /tmp/f pipe and back over to the client

**On ‘client’ side:**
```
$ nc host.example.com 1234
$ (shell prompt from host.example.com)
```
- just connect over netcat and you'll be presented with a shell.

## Share Your Screen With ffmpeg
[[ffmpeg]] lets you work with the audio and video made available while you are logged in. It's flexible enough to work with any video stream on your system including webcams and provide things like overlays. It's a command line only tool and is very robust.

To use it over a netcat session shared from you _the server_ to a remote connection _the viewer_ do this.

**For the server:**
```ffmpeg -f fbdev -i /dev/fb0 -f avi pipe:1 | nc -l -p 1234```

**For the viewer**
```nc 127.0.0.1 1234 | ffplay -i pipe:0```

# Metadata
**TAGS:** #Linux #Reference #UsefulCommands #Recipies 