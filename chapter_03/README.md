# Chapter 3


- Kubernetes can be ran and manged on cloud
- However if we wish to do it locally we can use *minikube*
    - it only deploys one cluster, so not ideal
    - included with Docker Desktop

## Kubernetes on a Cloud Provider

### Google Cloud Platform

- Hosts Kubernetes-as-a-Service called Google Kubernetes Engine (GKE)
- To get started:
    1. Set up a project and billing
    2. Create a cluster:
        > gcloud container clusters create kuar-cluster --num-nodes=3
    3. Get credentails for cluster:
        > gcloud container clusters get-credentials kuar-cluster

### Azure

- Offered as part of Azure Container Service
- Install Azure Cloud Shell to get started:
    1. Create a group
        > az group create --name=kuar --location=westus
    2. Create a cluster
        > az aks create --resource-group=kuar --name=kuar-cluster
    3. Get credentials for cluster:
        > az aks get-credetials --resource-group=kuar --name=kuar-cluster

### Amazon Web Services

- Offered as a service called Elastic Kubernetes Service (EKS)
- Requires the *eksctl* command line tool, once installed:
    1. Create a cluster:
        > eksctl create cluster

### Minikube

- Ran locally
- Uses a hypervisor on machine, on Linux/Mac it's VirtualBox, on Windows Hyper-V
! TODO: What is a hypervisor?
- To deploy:
    > minikube start
    > minikube stop
    > minikube delete

### Docker

- can be used to simulate different nodes
- uses the *kind* project -> still Work in Progress
> kind create cluster --wait 5m

## Kubernetes Client

- *kubectl* is a command-line tool for interacting with Kubernetes API
- can be used to manage most Kubernetes objects - Pods, ReplicaSets, Serices
! NOTE: For this example I will use GKE
- To check the version of the cluster:
    > kubectl version
    - Kubernetes tools are backward- and forward-compatible
- Next, run a simple diagnostic
    > kubectl get componentstatuses

| Component | Description |
| ----------- | ----------- |
| controller-manager | runs controllers that regulate behaviour in the cluster, like checking all replicas of a service are healthy |
| scheduler | responsible for placing different Pods onto different nodes in the cluster |
| etcd | storage for the cluster, where all API objects are stored |
- To list all the nodes:
    > kubectl get nodes
    - there are 2 type of nodes: 
        1. *control-plane* that contain containers like API server, scheduler, etc. -> used to manage the cluster
        2. *worker* nodes where the containers will run
- To get more info on a node:
    > kubectl describe nodes `Node Name`


## Cluster Components

- all components that make up the cluster run in the *kube-system* namespace

### Kubernetes Proxy

- routes network traffic to load-balanced services in the cluster
- must be present on every node in the cluster -> utilizes an API called DaemonSet for this
- we can see the proxies by running:
    > kubectl get daemonSets --namespace=kube-system kube-proxy
- ideally the kube-proxy container should run on all nodes in a cluster

### Kubernetes DNS

- naming and discovery for services defined in the cluster
- runs as a replicated service on the cluster 
- sometimes called *coredns*
    > kubectl get deployments --namespace=kube-system core-dns
    > kubectl get service --namespace=kube-system core-dns
        
    - the above shows the Cluster IP which has been populated in /etc/resolv.conf file in container
        