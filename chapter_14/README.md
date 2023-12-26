# RBAC

- Role Based Access Control is enabled on almost all of the new clusters
- It is a way to limit both access and ensure that only the right people can make the API calls they are supposed to
- Note: anyone who can run code on the cluster can obtain root privileges, for full security the Pods themselves should be isolated
- Every request to Kubernetes is first authenticated, this provides the identity of the caller making the request, this is done by a third-party (Azure AD) as Kubernetes does not have a built-in directory
- Once the request is authenticated, we go to authorization, this is the combination of the user, the resource (HTTP path), and action (verb), if they are allowed it goes through otherwise a 403 is returned

## Role-Based Access Control

### Identity in Kubernetes

- Every request is associated with an identity, even no identity has *system:unauthenticated*

### Role and Role-bindings

- A role is a set of abstract abilities
- Role binding is assigning it to one or more identities
- There are 2 pairs, one scoped to the namespace (Role and RoleBinding), and another scoped to the cluster (ClusterRole and ClusterRoleBinding)
- We cannot use namespaced roles for nonnamespaced resources (CRDs)
- An example is a role that gives the ability to create and modify Pods and Services

```
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: pod-and-services
rules:
- apiGroups: [""]
  resources: ["pods", "services"]
  verbs: ["create", "delete", "get", "list", "patch", "update", "watch"]
```

- Once the role is created a RoleBinding can be used to attach it to a user

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  namespace: default
  name: pods-and-services
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: alice
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: mydevs
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: pod-and-services
```

- Current built-in roles can be seen via:

> kubectl get clusterroles

- The built-in roles will can be changed, but the change will get overwritten when the cluster is restarted, to prevent this *rbac.authorization.kubernetes.io/autoupdate* must be set to false in the ClusterRole definition
- WARNING: By default system:unauthenticated is allowed to view API resources, to prevent this we must use *--anonymous-auth=false*

### Verbs

- Verbs are actions that can be performed

| Verb | HTTP Method | Description |
| ---- | ----------- | ----------- |
| create | POST | Create new resource |
| delete | DELETE | Delete existing resource |
| get | GET | Get resource |
| list | GET | List a collection of resources |
| patch | PATCH | Modify resource |
| update | PUT | Modify a resource via a complete object |
| watch | GET | Get streaming updates |
| proxy | GET | Connect to resource |

## Managing RBAC

### Testing Authorization

- A useful tool for testing access is *can-i*

> kubectl auth can-i create pods

> kubectl auth can-i get pods --subresource=logs

### Source Control

- *reconcile* is used like *apply* and it can be used to re-apply, or roll back to a set of binding
- We can use *--dry-run* to see changes before they are applied

