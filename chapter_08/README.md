# HTTP Load Balancing

- a critical part of any app is getting network traffic
- Service only operates at Layer 4 -> meaning it only forwards TCP and UDP
- HTTP - port 80
- HTTPS - port 443
- The HTTP-based LB in Kubernetes is called Ingress
- Ingress is split into 2 - A common resource specification and a controller implementation

## Contour

- there are many available Ingress controllers - Contour is one of them
- to install:
> kubectl apply -f https://projectcontour.io/quickstart/contour.yaml

- the above command will create a namespace called *projectcontour*
- inside the namespace is creates a deployment (two replicas) and an external-facing service of *type:LoadBalancer*

### DNS

- for Ingress to work well DNS entries have to be configuted 
- If the domain is *example.com* we will create an entry called *alpaca.example.com* 
- A records for IP addresses, CNAME records for hostname

## Ingress

- once the ingress controller is configured we can move forward
- first we create some 'backend' services to play with
> kubectl create deployment be-default --image=gcr.io/kuar-demo/kuard-amd64:blue --replicas=3 --port=8080

> kubectl expose deployment be-default

- finally we deploy a simple ingress that passes everything
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: simple-ingress
spec:
  defaultBackend:
    service:
      name: alpaca
      port:
        number: 8080
```