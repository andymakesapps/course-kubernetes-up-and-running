# Common Commands

## Namespaces

- used by Kubernetes to organize objects in the cluster
- think of them as a folder that holds a set of objects
- by default *kubectl* uses the *default* namespace
- to use a different one we pass the *--namespace* flag, or the *-n* flag
    - to interract with all namesapces use *--all-namespaces*

## Contexts

- to change the default namespace permananetly we use context
- gets recorded in kubectl config file -> $HOME./kube/config
- to create a new context:
> kubectl config set-context my-context --namespace=my-stuff
> kubectl config use-context my-context

## Viewing API Objects

- everything in Kubernetes is represented by a RESTful resource, called Kubernetes objects
    - REST = Representational State Transfer
- each object has an unique HTTP path
> https://your-k8s.com/api/v1/namespaces/default/pods/my-pod
- the above leads to a representation of a Pod in default namespace called my-pod
- kubectl makes HTTP requests to these URLs

### View Basics

> kubectl get *resource-name*
- the above is the basic viewing command

> kubectl get *resource-name* *obj-name*
- will return a specific resource

> -o wide/json/yaml
- is used to change the way the output is printed

> --no-headers
- is a common flag for pipes

- we can also get specific fields using JSONPath query language 
> kubectl get pods my-pod -o jsonpath --template={.status.podIP}
- will return the IP of the pod

> kubectl describe *resource-name* *obj-name*
- this is used to get more details of an object

> kubectl explain pods
- is used to get all supported fields

> --watch
- used to monitor state

## Create, Update, and Destroy

- objects are represented as JSON or YAML files

**Example:**
- we have a simple object in *obj.yaml*
- to create it:
> kubectl apply -f obj.yaml
    - we can run apply again if we made updates
    - *edit-last-applies*, *set-last-applied*, and *view-last-applied* can be also used as flags to see the history
- apply only modifies objects that are different from current cluster objects

> --dry-run
- can be used to check what changes will be made

- to interactively edit an object:
> kubectl edit *resource-name* *obj-name*

- to delete the object:
> kubectl delete -f obj.yaml
> kubectl delete *resource-name* *obj-name*

## Labeling and Annotating Objects
- labels and annotation are tags for objects
- to add a label to a pod called *bar*:
> kubectl label pod bar color=red
    - labels cannot be overwriten, to do so use *--overwrite*
- to remove the label:
> kubectl label pods bar color-

## Debugging
- to see logs for a container:
> kubectl logs *pod-name*
    - *-c* flag can be used if there are multiple containers
    - *-f* to stream the logs

- to execute a command on a container:
> kubectl exec -it *pod-name* -- bash

- files from the container are copied with:
> kubectl cp *pod-name*:*/path/to/remote/file* *path/to/local/file*

- to access pod via network
> kubectl port-forward *pod-name* 8080:80
    - sets up a connnection that ports 8080 traffic from local machine to 80 container

- to check events
> kubectl get events

- to see how resources are used
> kubectl top nodes

## Cluster Management
- we can cordon or drain a cluster
    - cordon = prevent future pods from being created
    - drain = remove any Pods currently running
> kubectl cordon
    - uncordon is used to revert it
> kubectl drain
