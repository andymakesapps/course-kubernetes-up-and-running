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
- this makes it easy to scale the programs that make up the service as it reduces the surface area of changes

### Easy Scaling for Applications

- Due to its immutable nature, Kubernetes can easily be scaled simply by defining the number of replicas in the config 

### Microservices

- Fun Fact: Research shows the ideal team size is the "two-pizza team", in other words six to eight people
- Kubernetes offers abstractions and APIs to achieve good team splits:
    - Pods = group of containers, can also group different images into containers
    - Services = Load Balancing, used to discover and isolate microservices from one another
    - Namespaces = provide isolation and Access Control for each microservice so that they can control the degree other services interact with it
    - Ingress = front end service 

### Separation

- A teamn can manage a cluster for hundreds maybe thousands of applications/teams
- KaaS (Kubernetes-as-a-Service) makes this easier as it is available on all public clouds
    - However, on clouds alpha features are usually disabled as the operator makes some decisions

## Abstractions

- Too often cloud APIs focus on mirroring infrastrucutre IT experts want (Virtual Machines) instead of what developers consume (applications)
- With Kubernetes moving applications is a matter of setting the right config

