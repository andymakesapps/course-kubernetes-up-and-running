# Extending Kubernetes

- there are other useful tools that can be deployed as API objects in a Kubernetes cluster
- extensions either add new functionality or limit the way users interact with the cluster
- should be careful with these plug-ins as they can be a vector for seeing all resources and secrets
- flow of an admisson controller and CustomResourceDefinitions:
> USER -> Authentication -> Admission Controll (Validate or Mutate) -> API server -> STORAGE

- Admission Controllers are used prior to the API being written in the storage 
- they can either reject or modify the request
- another extension that can be used with admission controllers is custom resources
- with them we can add new APIs
- to create a custom resource first we create a CustomResourceDefinition -> CRD
    - they are a meta resource = a resource that is the definition of another resource

```
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: loadtests.beta.kuar.com
spec:
  group: beta.kuar.com
  versions:
    - name: v1
      served: true
      storage: true
  scope: Namespaced
  names:
    plural: loadtests
    singular: loadtest
    kind: LoadTest
    shortNames:
    - lt
```

- it is Kubernetes object like
- has a metadata sub-object where we have the name
- However, the name has to be *Resource-Plural.Api-Group* to ensure uniqueness
- additionally we have a spec sub-object
    - apigroup must match suffix of CRD name

- if we were to run the command below there would be no output
> kubectl get loadtests

- however if we first run the below it will then work:
> kubectl create -f loadtest-resource.yaml

> kubectl get loadtests

- still it won't return anything because there are no LoadTest resources defined
```
apiVersion: beta.kuar.com/v1
kind: LoadTest
metadata:
  name: my-loadtest
spec:
  service: my-service
  scheme: https
  requestsPerSecond: 1000
  paths:
  - /index.html
  - /login.html
  - /shares/my-shares/
```

TO BE CONTINUED...