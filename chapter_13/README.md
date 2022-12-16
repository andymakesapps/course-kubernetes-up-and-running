# ConfigMaps and Secrets

- Ideally all deployments should be as generic as possible and not connected to the environment in any way (development should mirror porduction)
- ConfigMaps and Secrets allow us to pass configuration information and secrets (credentials or TLS certs) respectively

## ConfigMaps

- Kubernetes object that defines a small filesystem 
- Or a set of variables that can be used when defining the env or cmd line of container

### Creating ConfigMaps

- In this example we will have a file called *my-config.txt* that we want to make available in the Pod
- Next we can create the ConfigMap with:

> kubectl create configmap my-config --from-file=my-config.txt --from-literal=extra-param=extra-value --from-literal=another-param=another-value

### Uses

- There are several uses for ConfigMaps
    - Filesystem: a file is created for each key of the ConfigMap, the value is the content of the file
    - Environment Variable: used to dynamically set the value of env vars
    - Command-line argument: dynamically create command line for a container based value

## Secrets

- WARNING: By default Secrets are stored in plain text format in etcd on the cluster, Clusters Admins can view them, it is recommended to use user supplied encryption keys 

### Creating Secrets

- Secrets can be created with kubectl or via the API

> kubectl create secret generic kuard-tls --from-file=kuard.crt --from-file=kuard.key

- In order for applications or pods to consume the secrets they have to be mounted on that pod via volumes