# Images
Images are the base contents of what you expect to run with your application. They will usually consist of the userspace utils and system folder structure of a Linux distribution with nothing else installed at first. You might choose to expand the image by giving installing extra software on it. ^748b5d

They serve as templates for creating a [container](#^bc1eac)

What images _DO NOT_ include are networking/storage/cpu configurations and they are _NOT_ running processes.

# Containers
Containers are the product of an image being selected to run by [dockerd](#^73227f). They will have a state associated with them and may be interfaced with like another host on your network. This means you should be able to access a terminal on them and they may be serving applications over ports or sockets. ^bc1eac

# Docker Clients
The Docker Client is your interface do [dockerd](#^73227f) and will usually be the `docker` command line utility that most people use but can be anything that is capable of making REST calls to the docker API. ^89bf79

# Container Runtime

# Cgroups
Cgroups is a Linux kernel feature that limits, accounts for, and isolates the resource usage (CPU, memory, disk I/O, network, etc.) of a collection of processes. They are bound by the same criteria and associated with a set of parameters. Cgroups are how Docker isolates and allocate resources to containers.

Cgroups make the host system aware of a container's isolation from itself and other containers

# Namespaces

^0ff173

A namespace wraps a global system resource in an abstraction to make it appear to the processes within the namespace as though it has it's  own isolated instance of the resource. Changes to the assigned resource are visible to other processes that are members of the namespace, not to other processes.

Namespaces make the container aware of it's own isolation from the host and other containers.

# Dockerfile
A Dockerfile is a text file meant to be interpreted by [Docker Compose](#^10a3a8). The file specifies features of the [container](#^bc1eac) you would like created and gives you a single place to keep set things like storage/network/cpu settings. ^3e8818

It also allows you to run commands inside the container as soon as it gets created.

# Docker Daemon
This is the server component of docker that responds to requests from the [docker client](#^89bf79) for pulling/pushing [images](#^748b5d) to or from a [docker registry](#^1e22c2) and running [containers](#^bc1eac). ^73227f

# Layers

^276249

Layers are stackable units of storage associated with an [image](#^748b5d) that allow you to specify saved states you would like present in the eventual [container](#^bc1eac). I say stackable because they kind of work like snapshots. ^a1ef57

You might for example have an image layer that may be stacked on the base image and from there you might have several second layers for different applications of that image in the container you can ask it to create.

This adds to the modularity of docker and makes it even more light weight.

# Registries
Registries curate collections of [docker images](#^748b5d) for you to retrieve the base software needed for your applications. You'll usually have _Docker Hub_ configured as your registry which is where the docker client will request images when you use commands like `docker pull` or `docker run`.  ^1e22c2

# Docker Compose
Docker Compose is a [client](#^89bf79) that reads instructions from a text file that specifies an image and container settings to use when instantiating a container. ^10a3a8

It reads [docker files](#^3e8818) and makes the process scriptable and subject to other things you might like to use like version control.