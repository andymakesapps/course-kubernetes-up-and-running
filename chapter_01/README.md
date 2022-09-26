# Introduction

- Kubernetes is an open source orchestrator for deploying containerized applications
- introduced in 2014
- provides software to build and deploy reliable, scalable distributed systems

## Velocity

- key component in modern software dev
- not measured in raw speed, but number of things that can be shipped whilst maintaining a shighly available service
- Kubernetes does this by providing users with certain tools:

### Immutability

- once an artifact is created it does not change via user modification
- in mutable systems we can run *apt-get update* to make changes
- in immutable systems a whole new image is built for the service
- reason for building a new artifcat is the record it provides - easy to track differences
- additionally, a new image means the old is still there in case of a rollback

### Declarative Configuration

- everything in Kubernetes is a declarative config -> represents the desired state
- integrates well with GitOps conecpts where it becomes a read-only environemtn

### Self-Healing

- Kubernetes continously takes actions to match desired state
- recent work lead to the operator paradigm, allowing for more advanced logic to maintain, scale and heal a piece of software

## Scaling

- as a product grows there is need to scale both software and the team developing it -> Kubernetes does both

### Decoupling

- in a decoupling architecture each component is separated from other components
- this makes it easy to slave the programs that make up the service as it reduces the surface area of changes

### Easy Scaling for Applications

- Kubernetes includes automating slacing of resources 

TO BE CONTINUED...