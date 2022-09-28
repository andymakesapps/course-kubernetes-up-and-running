# Service Discovery

- Kubernetes is quite dynamic, however how do we find everything when it's all chaning?
- Service-discovery tools help us find which processes are listening at which address for which services
- a traditional example of this is the Domain Name System - DNS

## Service Object

- service discovery in Kubernetes is done via Service object
- we used *kubectl run* to create a deployment, we can now use *kubectl expose* to create a service
> kubectl create deployment alpaca-prod --image=gcr.io/kuar-demo/kuard-amd64:blue --port=8080
> kubectl scale deployment alpaca-prod --replicas 3
> kubectl expose deployment alpaca-prod

- the expose command will map it to port 8080
- furthermore the cluster will now have a Cluster IP which is used for load balancing
- to interact with it we can port-forward:
> ALPACA_POD=$(kubectl get pods -l app=alpaca -o jsonpath='{.items[0].metadata.name}')
> kubectl port-forward $ALPACA_POD 48858:8080

- we can now access the pod via *http://localhost:48858*

### Service DNS

- since the cluster IP is virtual, it is stable, and it is appropriate to give it a DNS address
- Kubernetes offers a DNS service exposed to Pods running in the cluster
- the full DNS name of the alpaca-prod looks like so:
> alpaca-prod.default.svc.cluser.local.

```
alpaca-prod     = name of the service
default         = the namespace
svc             = showing it is a service
cluster.local.  = base domain name for most clusters
```

- when refering to the service in own namespace we can just use *alpaca-prod*

### Readiness Checks

- sometimes it takes a few seconds or minutes for a service to be ready
- we can add a readiness check to a Pod like so:

> kubectl edit deployment/alpaca-prod

- next we add the following section
```
spec:
    ...
    template:
        ...
        spec:
            containers:
                ...
                name: alpaca-prod
                readinessProbe:
                    httpGet:
                        path: /ready
                        port: 8080
                    periodSeconds: 2
                    initialDelaySeconds: 0
                    failureThreshold: 3
                    successThreshold: 1
```
- this will set up a check via HTTP GET to /ready on port 8080 every 2 seconds
- if 3 successive checks fail the Pod is considered not ready
- upon a deployment definition like the one above the Pod will be deleted and re-created 

## Traffic Outside the Cluster

- so far we saw examples of exposing services inside of a cluster
- we can use NodePorts to allow new traffic in
- using them the system picks up a port, and every node in the cluster forwards traffic to that port
- to do this we edit our service:

> kubectl edit service alpaca-prod

- and we change spec.type to NodePort
- this can also be done during creation via *kubectl expose ADDITIONAL FLAGS --type=NodePort*

## Load Balancer Integration

- similarly, to integrate with external load balancers we edit our service and chang spec.type to LoadBalancer
- this will expose it to the public internet
- if using a cloud provider the load balancer will be handled by it
- internal load balancers can also be used however they are added via annotations -> as the service is quite new
- for GCP we need to add the following in the Service resource:
> cloud.google.com/load-balancer-type: "Internal"

```
...
metadata:
    ...
    name: some-service
    annotations:
        cloud.google.com/load-balancer-type: "Internal"
...
```

## Advanced Details

- as Kubernetes is an extensible systems there are layers for more advnaced integrations

### Endpoints

- used when we want to deploy a service without using a Cluster IP
- for every Service object Kubernetes creates a buddy Endpoint object containing the IP of that service
> kubectl get endpoints alpaca-prod

### Manual Service Discovery

- instead of using Service object we can utilize the selector based on labels
> kubectl get pods -o wide --selector=app=alpaca

### kube-proxy and Cluster IPs

- Cluster IPs are stable virtual IPs that load balance traffic across all endpoints in a service
- this is done by a component running in every node in the cluster called *kube-proxy*

