# ReplicaSets

- so far we saw containers run as Pods, but those were one-off singletons
- usually we want multiple replicas of a container running for various reasons:
    - redundancy 
    - scale
    - sharding 
- a ReplicaSet is a replicated set of Pods acting as a single entity 
- it acts as a cluster-wide Pod manager
- managing the replicated Pods is called reconciliation loop

## Reconciliation Loops

- the main concept behind is the notion of desired state vs observed/current state
- desired state = what we want
    - with a ReplicaSet it means the desired number of replicas and the definition of the Pod to replicate
- current state = what the system currently is
- reconciliation loop is constantly running, observing current state and trying to make it match desired state

## Pods and ReplicaSets

- relationship between Pods and ReplicaSets is loosely coupled due to Kubernetes's decoupling nature
- ReplicaSets create and manage pods but they do not own them

### Existing Containers

- Imagine we have a Pod already set up, maybe with a Load Balancer
- We want to add a ReplicaSet to it, if ReplicaSet owned them then we would need to delete the Pod and launch it again causing disruption
- Instead we can simply configure a ReplicaSet and it will adopt the current Pod

### Quarantine

- If health checks are failing there is an option within ReplicaSets to isolate that sick Pod
- It will be removed from the ReplicaSet allowing devs to debug it, whilst a new copy will take its place

## ReplicaSet Spec

- like all objects in Kubernetes, ReplicaSets have a specificaiton 
- *metadata.name* must be unique, whilst the rest is defined under *spec*

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  labels:
    app: kuard
    version: "2"
  name: kuard
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kuard
      version: "2"
  template:
    metadata:
      labels:
        app: kuard
        version: "2"
    spec:
      containers:
        - name: kuard
          image: "gcr.io/kuar-demo/kuard-amd64:green"
```

### Pod Template

- If the current state is different from the desired state the ReplicaSet will fix the issue using the provided Pod template to deploy a new Pod
- This template is esentially the Pod deployment seen previously (chapter 5)

```
template:
  metadata:
    labels:
      app: helloworld
      version: v1
  spec:
    containers:
      - name: helloworld
        image: kelseyhightower/helloworld:v1
        ports:
          - containerPort: 80
```

### Labels

- ReplicaSets use labels to filter out which Pods to use

## Creating ReplicaSets
- Pods can be created by the *kubectl apply* command
- Once the ReplicaSet in our example is deployed it will notice there are no kuard Pods running and start deploying them
- To find out which ReplicaSet owns a pod we can use:

> kubectl get pods pod-name -o=jsonpath='{.metadata.ownerReferences[0].name}'

- To find a set of Pods managed by a ReplicaSet we must first get the label used, then:

> kubectl get pods -l app=kuard,version=2

    - Where *-l* is shorthand for *--selector* flag
    - This is the same querry ReplicaSets use to find the number of Pods

## Scaling ReplicaSets

- We can scale by updating the *spec.replicas* key in the yaml
- An interactive method can also be used:

> kubectl scale replicasets kuard --replicas=4

- However, any change via the command line must also be recreated in the text configuration, otherwise we run the risk of the yaml specification being applied after an update or rolling replace

### Autoscaling

- In order to scale a ReplicaSet we can either use Horizontal Pod Autoscaler or Vertical Pod Autoscaler, both used to scale horizontally or vertically respectively 
- Autoscaling however requires the presence of *metrics-server*, it collects metrics and provides an API for HPA to use
- To find out if metrics-server is present we can use:

> kubectl get pods --namespace=kube-system 

- To scale based on CPU usage:

> kubectl autoscale rs kuard --min=2 --max=5 --cpu-percent=80

- this will create between 2 and 5 Pods based on a CPU usage of 80%
- To get details on the Horizontal Pod Autoscaler:

> kubectl get hpa

## Deleting ReplicaSets

- once we are done we can delete the set which will also delete the Pod
> kubectl delete rs kuard

- if we don't want to delete the Pods we can use:
> kubectl delete rs kuard --cascade=false