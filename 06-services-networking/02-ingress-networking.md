# Ingress Networking

Q. Which namespace ingress resources deployed in?
```yaml
$ k get ingress --all-namespaces
```

Q. What is the Host configured on the Ingress Resource? The host entry defines the domain name that users use to reach the application like www.google.com.

```yaml
$ kubectl describe ingress --namespace app-space
```
Look at the Host under the Rules section.

Q. If the requirement does not match any of the configured paths in the Ingress, to which service are the requests forwarded?
```yaml
$ kubectl describe ingress --namespace app-space 
```
Examine the Default backend field. If it displays <default>, proceed to inspect the ingress controller's manifest by executing 
```yaml
$ kubectl get deploy ingress-nginx-controller -n ingress-nginx -o yaml
```
In the manifest, search for the argument --default-backend-service.

Change the URLs at which the applications are made available. Make the video application available at /stream. Update the path of video-service from /watch to /stream
```yaml
$ kubectl edit ingress -n app-space
```

Identify and implement the best approach to making this application available on the ingress controller and test to make sure its working.

Q. New lab
```yaml
$ k create namespace ingress-nginx
```
The NGINX Ingress Controller requires a ConfigMap object. Create a ConfigMap object with name ingress-nginx-controller in the ingress-nginx namespace.
```yaml
$ kubectl create configmap ingress-nginx-controller --namespace ingress-nginx
```
The NGINX Ingress Controller requires two ServiceAccounts. Create both ServiceAccount with name ingress-nginx and ingress-nginx-admission in the ingress-nginx namespace. Use the spec provided below.
Name: ingress-nginx
Name: ingress-nginx-admission
```yaml
$ kubectl create serviceaccount ingress-nginx --namespace ingress-nginx
$ kubectl create serviceaccount ingress-nginx-admission --namespace ingress-nginx
```
Create the Roles, RoleBindings, ClusterRoles, and ClusterRoleBindings for the ServiceAccount. Check it out!!

