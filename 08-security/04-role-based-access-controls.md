# RBAC

Q. Inspect the environment and identify the authorization modes configured on the cluster. Check the kube-apiserver settings.

A. Use the command below and look for --authorization-mode.
```yaml
$ kubectl describe pod kube-apiserver-controlplane -n kube-system
```

Q. How many roles exist in the default namespace.
A. 
```yaml
$ kubectl get roles                   #---> default namespaces
$ kubectl get roles --all-namespaces  #---> all namespaces
```

Q. What are the resources the kube-proxy role in the kube-system namespace is given access to?
```yaml
$ kubectl describe roles kube-proxy -n kube-system
```

Q. Which account is the kube-proxy role assigned to?
A. 
```yaml
$ kubectl describe rolebinding kube-proxy -n kube-system
```

Q. Check if the dev-user have access to list pods.
A. 
```yaml
$ kubectl get pods --as dev-user
```

Q. Create the necessary roles and role bindings required for the dev-user to create, list and delete pods in the default namespace. Use the given spec:
- Role: developer
- Role Resources: pods
- Role Actions: list, create, delete
- RoleBinding: dev-user-binding

A.
Imperative way:

To create a Role:- 
```yaml
kubectl create role developer --namespace=default --verb=list,create,delete --resource=pods
```

To create a RoleBinding:- 
```yaml
kubectl create rolebinding dev-user-binding --namespace=default --role=developer --user=dev-user
```
Declarative way:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list","create","delete"]

---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-binding
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

kubectl edit role developer -n blue
```yaml
- apiGroups:
  - apps
  resources:
  - deployments
  verbs:
  - create
```
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
  namespace: blue
rules:
- apiGroups:
  - apps
  resourceNames:
  - dark-blue-app
  resources:
  - pods
  verbs:
  - get
  - watch
  - create
  - delete
- apiGroups:
  - apps
  resources:
  - deployments
  verbs:
  - create
```