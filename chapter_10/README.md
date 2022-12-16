# Deployments

- They exist to allow easy release of new versions
- Easily move from one version to the next
- No downtime as all updates happen server-side

## First Deployment

- like all objects Deployments are defined via a YAML file

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kuard
  labels:
    run: kuard
spec:
  selector:
    matchLabels:
      run: kuard
  replicas: 1
  template:
    metadata:
      labels:
        run: kuard
    spec:
      containers:
      - name: kuard
        image: gcr.io/kuar-demo/kuard-amd64:blue
```

- As with all Kubernetes relationships this is defined by a label and a LabelSelector, to get more information we can use:

> kubectl get deployments kuard -o jsonpath --template {.spec.selector.matchLabels}

- From here we can see that the *kuard* deployment is managing deployments with the label run=kuard

> kubectl get replicasets --selector=run=kuard

- The Deployment manages the ReplicaSet, so if we want to scale the pods we can use:

> kubectl scale deployments kuard --replicas=2

- However, we cannot scale from the ReplicaSet, because the Deployment is top-level and will overwrite the change
- We can if we want to delete the Deployment with *--cascade=false* and that will leave us with a ReplicaSet which we can control
- The structure of a Deployment manifest is similar to that of a ReplicaSet, in addition there is a section for strategy

```
...
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
...
```

- There are 2 update types, RollingUpdate and Recreate

## Managing Deployments

- To get information on a deployment we can use

> kubectl describe deployments kuard

- The important bits from here are OldReplicaSets and NewReplicaSets
- If a roll out has completed OldReplicaSets will be set to *None*, whereas during a roll out both will have a value
- We can also get information on the roll out by using

> kubectl rollout history

> kubectl rollout status

## Updating Deployments

- the 2 most common updates are scaling and application updates

### Scaling

- Although *kubectl scale* can be used, best practice is to update the YAML file and deploy from there

```
...
spec:
  replicas: 3
...
```

- re-running *kubectl apply* will get this implemented

### Updating Container Image

- Similarly the container image in spec can be updated:

```
...
      containers:
      - image: gcr.io/kuar-demo/kuard-amd64:green
        imagePullPolicy: Always
...
```

- an annotation should also be added to describe the change

```
...
spec:
  ...
  template:
    metadata:
      annotations:
        kubernetes.io/change-cause: "Update to green kuard"
...
```

- the annotation should be added to the template and not the deployment itself
- this update will trigger a roll out that can be monitored via:

> kubectl rollout status deployments kuard 

## Deployment Strategies 

### Recreate

- the simpler of the two, it terminates all of the Pods in the ReplicaSet and allows new ones to be created
- it will result in downtime

### RollingUpdate

- slower, but recommended for user facing applications
- it deploys new ones and allows old ones to be deleted

