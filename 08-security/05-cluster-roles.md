# Cluster Roles
The resources in k8s are categorized into namespaced and cluster scoped. For example, pods, rs, jobs, deploymets, svc, secrets, role,rolebinding, configmaps, PVC are namespaced. And nodes, PV, clusterroles, clusterrolebindings, namespaces, vertificatesigningrequests are cluster scoped.

```yaml
$ kubectl api-resources --namespaced=true
$ kubectl api-resources --namespaced=false
```
To authorize users to use namespace scoped resources, roles and rolebindings are used.

To authorize users to use cluster scoped resources, clusterroles and clusterrolebindings are used. 

Cluster Admin
 - can view Nodes
 - can create Nodes
 - can delete Nodes

Storage Admin
 - can view PVs
 - can create PVc
 - can delete PVCs

cluster-admin-role.yaml
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
    name: cluster-administrator
rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["lsit", "get", "create", "delete"]
```
```yaml
$ kubectl create -f cluster-admin-role.yaml
$ kubectl get clusterroles --no-headers | wc -l  #---> How many clusterroles present
$ kubectl get clusterroles --no-headers  -o json | jq '.items | length'
```

cluster-admin-role-binding.yaml
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
    name: cluster-admin-role-binding
subjects:
- kind: User
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
roleRef:
    kind: ClusterRole
    name: cluster-administrator
    apiGroup: rbac.authorization.k8s.io
```
```yaml
$ kubectl create -f cluster-admin-role-binding.yaml
$ kubectl get clusterrolebindings --no-headers | wc -l #---> How many clusterrolebindings present
$ kubectl describe clusterrole cluster-admin
$ kubectl describe clusterrolebindings cluster-admin
```

Cluster role can be used for authorizing namespace resources as well. In that case the user will have access to the resources across all the namespaces.

Q. A new user michelle joined the team. She will be focusing on the nodes in the cluster. Create the required ClusterRoles and ClusterRoleBindings so she gets access to the nodes.

Imperative
```yaml
$ kubectl create clusterrole nodesreader --verb=* --resource=nodes
$ kubectl create clusterrolebinding nodes-admin --clusterrole=nodesreader --user=michelle
$ kubectl auth can-i list nodes --as michelle
$ kubectl auth can-i list nodes --as michelle --all-namespaces
```
Declarative
```yaml
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: node-admin
rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["get", "watch", "list", "create", "delete"]

---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: michelle-binding
subjects:
- kind: User
  name: michelle
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: node-admin
  apiGroup: rbac.authorization.k8s.io
```

Q. michelle's responsibilities are growing and now she will be responsible for storage as well. Create the required ClusterRoles and ClusterRoleBindings to allow her access to Storage.
Get the API groups and resource names from command kubectl api-resources. Use the given spec:
- ClusterRole: storage-admin
- Resource: persistentvolumes
- Resource: storageclasses
- ClusterRoleBinding: michelle-storage-admin
- ClusterRoleBinding Subject: michelle

```yaml
#### storage-admin-clusterrole.yaml ####
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: storage-admin
rules:
- apiGroups: [""]
  resources: ["persistentvolumes"]
  verbs: ["get", "watch", "list", "create", "delete"]
- apiGroups: [""]
  resources: ["storageclasses"]
  verbs: ["get", "watch", "list", "create", "delete"]
```

```yaml
#### storage-admin-clusterrolebinding.yaml ####
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: michelle-storage-admin
subjects:
- kind: User
  name: michelle
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: storage-admin
  apiGroup: rbac.authorization.k8s.io
```