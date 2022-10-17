Start here if you already kinda know what Docker is and why people are using it. Also, this note expects that you already know that images usually consist of a base Linux system that will be used to run your application. The image might already have the application coupled with the base OS if you built and saved that image. You should also know that when someone talks about a container, they are referring to a running instance of an image.

# Docker Architecture
Docker is designed around a client-server architecture where your system will be running some Docker client that communicates with the Docker daemon running on your machine. This allows you to decouple the functionality of requests made by the client from actions carried out by the server, giving you the flexibility to connect your docker client to a remote Docker server that does all the cool DevOps shit away from you workstation. It also gives you choices in clients and how you want to interact with the service. I hope you already realize how that's gonna be bad ass when we know more about this stuff.

## Some Docker Jargon To Know
- **dockerd:** This is the docker daemon, it will listen locally over UNIX domain sockets or remotely over a port for REST API calls.
- **docker:** The client that comes with Docker when you install it from packages. 
- **Docker Registries:** This is where your Docker images can be stored or retrieved from. You'll usually have _Docker Hub_ configured as your registry which is where the docker client will request images when you use commands like `docker pull` or `docker run`. 
- **containerd:** This is the container runtime used by not only docker but other projects like kubernetes for handling container operations like attaching storage and handling networking.

This should be the picture you're starting to get in your head about the operation. It misses the use of the container runtime but... that's not really used until you're starting to really DevOps things. This is adequate for the process running a stand-alone container on something like your workstation

![](./Images/Pasted%20image%2020211030105928.png)

# Installing Docker
Getting this done is gonna vary a little bit between distributions but, in short this is what will need to happen. Before you start, make sure to install the `overlay2` storage driver. This doesn't require any configuration and it lets you write to the writable layer of running containers from your host instead of having to get a session inside the container.

**NOTE:** You have choices in which storage driver you'd like to install and other drivers like zfs will give you cool things like snapshotting but will also require some configuration on your part

- Package repos that supply the Docker installer need to be configured on your system.
- Install the Docker service, client and the container runtime service.
- Add users to the _docker group_ on your system
- Start/Enable the docker service

