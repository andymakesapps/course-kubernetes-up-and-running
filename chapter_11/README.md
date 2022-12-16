# DaemonSets

- Deployments and ReplicaSets create a service with multiple instances for redundancy
- DaemonSets are used to set up a Daemon or Agent on every node
    - ReplicaSets -> use when app is decoupled from node and you can run multiple copies without special consideration
    - DaemonSets -> single copy of application must run on all or a subset of nodes in the cluster
