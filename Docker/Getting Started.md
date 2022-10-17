We'll start by building a container image from a [Dockerfile](Docker Glossary.md^#Dockerfile). The image will host a simple node.js app and from there we will:
- Make changes to the app
- Share the app as a container
- Get our changes to persist
- Use bind mounts in our app container
- Host apps across multiple containers

# Creating A Container Image
First, [Download the App contents](https://github.com/docker/getting-started/tree/master/app). from github and extract it into your working directory

Create this [Dockerfile](Docker Glossary.md^#Dockerfile) simply named _Dockerfile_ in the working dir.

```Dockerfile
FROM node:12-alpine
RUN apk add --no-cache python g++ make
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
```
## Inside The Dockerfile
The first line `FROM node:12-alpine` instructs docker to download the _node:12-alpine_ image from the [registry](Docker%20Glossary.md#Regisstries). This will be an base alpine Linux image with nodejs installed.

`RUN apk add --no-cache python g++ make` will cause the [container](Docker%20Glossary.md#^bc1eac) to run the apk command to install python, a c++ compiler and make.

`WORKDIR /app` sets the directory that all further commands will be run from inside the container to the app folder in the root directory.

`COPY . .` will copy all of the shit from the directory we are in on the host into the _WORKDIR_ folder that we just set in the container.

`RUN yarn install --production` is a nodejs command run from _WORKDIR_ where we just copied our nodejs app to. The command installs the app.

`CMD ["node", "src/index.js"]` runs the command `node src/index.js` which will start the nodejs application on our container

**NOTE: While the RUN and CMD lines seem to be accomplishing the same thing, the difference is that RUN will save the state of the container after running the command to create a new [layer](Docker%20Glossary.md#Layers) while CMD simple runs the commands from it's arguments**

## Build the image
Use the build the `docker build` command to build the container image from the _Dockerfile_

```
docker build -t getting-started .
```
the [-t option](docker%20build.md#^6e010a) specifies a [tag name](docker%20build.md##Tag%20an%20image%20(-t)) for the container to use. The tag is just a human readable name for us to refer to the container by.

## Run The Container
All that's left to do now is to create a container from the image we just created and try it out.
Use the [docker run](docker%20run.md#Docker%20Run) command like this
```docker run -dp 3000:3000 getting-started```

[-d](docker%20run.md#^63b638)] to run the container detached from our terminal

[-p](docker%20run.md#^a0d3de) to publish ports _3000_ on the container to port _3000_ on our host

with _getting-started_ as the tag name we are able to address the image by.

**NOTE: For more on the -p option [see here](docker%20run.md##Publish%20or%20expose%20port%20)**