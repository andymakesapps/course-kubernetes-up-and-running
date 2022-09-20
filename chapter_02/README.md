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