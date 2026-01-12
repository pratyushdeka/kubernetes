# Service Account

Two types of service accounts
- User account (used by humans)
    - **admin** (administrator access to acces the cluster to perform administrative tasks etc.)
    - **developer** (access cluster to deploy applications etc.)
- Service account (used by applications, systems to interact with the cluster)

For example, 
- a monitoring application like Prometheus uses a service account to pull the Kubernetes API for performance metrics.
- Jenkins uses service account to deploy apps on the k8s cluster

```yaml
$ kubectl create serviceaccount dashboard-sa          #---> create service account
$ kubectl get serviceaccount                        #---> List all the service account
$ kubectl describe serviceaccount dashboard-sa        #---> Describe the respective service account
```

When the service account created, it also creates a token automatically. The service account token is what must be used by the external application while authenticating to K8s API. The token is stored as secret object. 

To view the token, run
```yaml
$ kubectl describe secret dashboard-sa-token-xxxxx
```

You can create a service account, assign the write permissions using role-based access control mechanisms, and export your service account tokens, and use it to configure your third-party application to authenticate to the Kubernetes API.

### ***Revisit this below part from the kubernetes documentation***

By deafault, when a pod is created, a volume is automatically created from the secret named default token
```yaml
$ kubectl describe pod my-k8s-dashboard
$ kubectl exec -it my-k8s-dashboard -- ls /var/run/secrets/kubernetes.io/serviceaccount #---> list secret mounted
$ kubectl exec -it my-k8s-dashboard cat /var/run/secrets/kubernetes.io/serviceaccount/token  #---> display the default token
```
pod-definition.yaml with Service account
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-k8s-dashboard
spec:
    containers:
        - name: my-k8s-dashboard
          image: my-k8s-dashboard
    serviceAccountName: dashboard-sa #---> 
```


How many service account present in the given namespace
```
$ kubectl get sa
```
What is the secret token used by the default service account?
```
$ kubectl describe serviceaccount default
```