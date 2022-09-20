# Chapter 2


## Container Images

- A binary package that encapsulates all of the files necessary to run a program inside of an OS container
- Either build from local filesystem or built from preexisiting image from a *container registry*

- Most popular container image format is the Docker image format
- Standard from the OCI (Open Container Initative)
- Overlay filesystem = each layer adds or removes files from preceding layer
- Image in this case doesn't mean single file, but a specification for a manifest file that points to other files

**Example:**
```
.
└── container A: a base operating system only, such as Debian
    └── container B: build upon #A, by adding Ruby v2.1.10
    └── container C: build upon #A, by adding Golang v1.6
```

- Here we have container A, B, and C
- B and C are forked from A, next we build on B

```
. (continuing from above)
└── container B: build upon #A, by adding Ruby v2.1.10
    └── container D: build upon #B, by adding Rails v4.2.6
    └── container E: build upon #B, by adding Rails v3.2.x
```
- Each container image adds on to the other one
- Container images usually have a container config file, which gives instructions on how to set up the container env and app entry point
- We have 2 types of containers:
    1. System containers
        - mimic virtual machines, run full boot process
        - include *ssh*, *cron*, and *syslog*
        - bad practice, used to be used when Docker was new
    2. Application containers
        - run a single program
        - provides granurality 

## Building Application Images with Docker

### Dockerfile

- used to automate creation of Docker container image

*Example:*
- We will build a Node.js program
- We have 2 files -> package.json and server.js
- To package it we create 2 more files -> .dockerignore and Dockerfile
    Dockerfile explanation:
        - Every Dockerfile builds on other container images. The first line says we start from node:16 image on Docker Hub -> Node.js 16 image
        https://hub.docker.com/_/node
        - Next line sets a working dir for all following commands
        - The next section initializes dependencies for Node.js
            - First, copy package files into image
            - RUN runs a command in the container to install dependencies
        - Next we copy everything else, except files from the .dockerignore file
        - Finally we specify the command that should run when container runs
- To build the container we run:
> docker build -t simple-node .

NOTE
: If on Mac, run the Docker desktop app first, otherwise it won't be able to connect to docker daemon
- To run the image we can use:
> docker run --rm -p 3000:3000 simple-node

### Optimization

```
.
└── layer A: contains a large file named 'BigFile'
    └── layer B: removes 'BigFile'
        └── layer C: builds on B by adding a static binary
```
- Container C still contains BigFile because it is part of layer A

Case I
```
.
└── layer A: contains a base OS
    └── layer B: adds source code server.js
        └── layer C: installs the 'node' package
```
Case II
```
.
└── layer A: contains a base OS
    └── layer B: installs the 'node' package
        └── layer C: adds source code server.js
```
- If server.js changes Case 1 will have to pull and push both server.js and node, whereas Case 2 can stay the same
- Best practice to order layers from least likely to change to most likely to change

## Remote Registry
- Instead of exporting all images to all clusters we should use a remote registry
- Can use both public and private (require auth to access)
- To push an image we need to login via:
> docker login
- GCP uses GCR (Google Container Registry)
- Alternativelly, we can use Docker Hub, we can also tag images
> docker tag kuard gcr.io/kuar-demo/kuard-amd64:blue
> docker push gcr.io/kuar-demo/kuard-amd64:blue

## Running Containers
- In Kubernetes containers are launched by a daemon on each node called the *kubelet*
- However, we can get started with docker
> docker run -d --name kuard --publish 8080:8080 gcr.io/kuar-demo/kuard-amd64:blue
- The command starts kuard and maps port 8080 from local machine to 8080 in container -> port fowarding
- -d lets it run in the background
- --name gives it a friendly name

## Clean up
- Once we are done building we can delete with:
> docker rmi <tag-name>
or
> docker rmi <image-id>
- Creating an image will make it live in the sys forever unless deleted
> docker images
- The above can be used to list current images OR
> docker system prune
- removes all images