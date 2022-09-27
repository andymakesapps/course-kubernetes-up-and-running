# Labels and Annotations

- both are fundamental concepts esential when sclaing apps in both size and complexity
- labels are key/value pairs that can be attached to Kubernetes objects, ideal for grouping or identifying info
- annotations provide a storage system that resembles lables, also key/value pairs but used for querying, filtering, and differentiating Pods from each other

## Labels

- key/value string pairs
- used for grouping objects and providing separation as well as hierarchy 
- the Label Key has two parts
    - optional prefix = must be DNS subdomain with 253-character limit
    - name = required and must have a max length of 63 characters, must start and end with alphanumeric character and allow -, _, and .
- Label Values have the same rules as Label Key Names

### Applying Labels

> kubectl run alpaca-prod --image=gcr.io/kuar-demo/kuard-amd64:blue --replicas=2 --labels="ver=1,app=alpaca,env=prod"

- here we have created an alpaca-prod deployment and attached ver, app, and env labels

> kubectl run alpaca-test --image=gcr.io/kuar-demo/kuard-amd64:green -- replicas=1 --labels="ver=2,app=alpaca,env=test"

- similarlly we create an alpaca-test deployment with different label values

### Modifying Lables

- to update a label we use:

> kubectl label deployments alpaca-test "canary=true"

- we can also use -L to show a label value as a column:

> kubectl get deployments -L canary

- to remove a label:

> kubectl label deployments alpaca-test "canary-"

### Label Selectors

- used to filter Kubernetes objects based on a set of labels

> kubectl get pods --show-labels

- will return all pods and their labels
- there will be an extra label *pod-template-hash*, this is generated by the deployment to keep track of Pods generated from which template version
- to get all pods which have ver 2 we can use:

> kubectl get pods --selector="ver=2"

- if we add a comma it will act as an AND logical operator, returning if both satisfy:

> kubectl get pods --selector="app=bandicoot,ver=2"

- the OR operator can be implemented as such:

> kubectl get pods --selector="app in (alpaca,bandicoot)"

- to get if the label exists:

> kubectl get deployments --selector="canary"

- the negative of the above is as follows:

```
=   <->  !=
in  <->  notin
key <->  !key
```

### Label Selectors in API Objects

- a Kubernetes objects uses label selector to refer to a set of other Kubernetes objects

> app=alpaca,ver in (1,2)

- would translate to the API as such:

```
...
selector:
    matchLabels:
        app: alpaca
    matchExpressions:
        - {key: ver, operator: In, values: [1, 2]}
...
```

- Negation is implemented by NotIn
- the older version of the selector only supports =

```
...
selector:
    app: alpaca
    ver: 1
...
```

## Annotations

- an additional place to store metadata where its sole purpose is assisting tools and libraries
- used to provide extra information as to where an object comes from
- as they are similar to labels there is some overlap
    - if in doubt, use annotations and promote them to a label if a selector is required
- they can also include release, build, or image information which is not always appropriate for labels
- used primarly in rolling deployments
- not a database, only good for small bits of data
- structure is similar to a label however as they are used to send info the *namespace* use is more important
- they are defined in the metadata section of a Kubernetes object:

```
...
metadata:
    annotations:
        example.com/icon-url: "https://example.com/icon.png"
...
```