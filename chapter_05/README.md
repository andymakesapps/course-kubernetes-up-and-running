# Pods

- often we want our apps to run on a single machine
- if we have 2 apps, one is a webserver the other is a git sync, and both used the same fileshare we would want them separate but on the same machine
- Kubernetes groups containers into a single atomic unit called a Pod
    - name comes from the whale theme of Docker, a group of whales is a pod

## Pods in Kubernetes

- Pod is a collection of application containers and volumes running in the same exec env
- Pod is the smallest deployable artifact in a cluster -> NOT containers
- Apps in different pods are isolated
- If 2 apps can work independently they should be put into different Pods
    - For example, a WordPress container and a MySQL database
- Pods are described in a Pod manifest -> a test-file representation of the Kubernetes API object 
    - declarative configuration = desired state is written and recorded
    - impoerative configuration = take a series of action to build system
- The manifest is accepted and processed by the Kubernetes API and stored in etcd

### Pod Creation

*Pod Creation - kubectl*
- To create a pod we use:
> kubectl run kuard --generator=run-pod/v1 --image=gcr.io/kuar-demo/kuar/amd64:blue

- to see the status of the pod:
> kubectl get pods

- and to remove it:
> kubectl delete pods/kuard

*Pod Creation - Manifest*
- can be YAML or JSON, but YAML is prefered as it is more human readable and can have comments
- should include a few key fields, such as a metadata section for the Pod and labels description and a spec section for the container in the pod
- to deploy a kuard pod we can first create:
```
#kuard-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: kuard
spec:
  containers:
    - image: gcr.io/kuar-demo/kuard-amd64:blue
      name: kuard
      ports:
        - containerPort: 8080
          name: http
          protocol: TCP
```
- then we deploy it via:
> kubectl apply -f kuard-pod.yaml

- once it is deployed the kubelet daemon will monitor it
- to see a better output of kubectl we can use *-o wide* 
- For details on the object we can add *-o json* or *-o yaml*
- and to enable verbosity *--v=10*

- To get pod details:
> kubectl describe pods kuard

- finally, to delete the pod:
> kubectl delete -f kuard-pod.yaml

### Accessing Pods

- in order to access logs on the pod we use:
> kubectl logs kuard

    - adding *-f* will allow us to stream the logs
    - with *--previous* we get logs of the prebious container that ran

- to execute commands directly we use:
> kubectl exec kuard -- date

- or for an interactive session:
> kubectl exec -it kuard -- ash



