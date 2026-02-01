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
```

Cluster role can be used for authorizing namespace resources as well. In that case the user will have access to the resources across all the namespaces.