# Secure Kubernetes

What are the risks and what measures do you need to take to secure the cluster? 

As we already know kube-apiserver is at the center of all operations within kubernetes. Two types of decisions need to make 
- Who can access kube-apiserver?
- What can they do?

## Authentication 
Authentication mechanisms defines who can access the kube-apiserver.
- static token file
- certificates
- external authentication providers - LDAP
- service accounts

## Authorization
Once authenticated, authorization mechanisms defines what can they do with the cluster.
- RBAC authorization (Role based access control)
- ABAC authorization (Attribute based access control)
- Node authorization
- Webhook mode

## TLS Certificates
All communication with the cluster between the various components such as etcd cluster, the kube-controller, manager, scheduler, API server, as well as those running on the worker nodes such as the kubelet and the kube-proxy is secured using TLS certificates. 

## KubeConfig
KubeConfig file path is $HOME/.kube/config and consist of three parts
- Clusters (Development, Production, Google)
- Contexts - marry cluster and users together (Admin@Production, Dev@Google)
- Users (Admin, Dev user, Prod user)

```yaml
apiVersion: v1
kind: Config
clusters:
- name: my-kube-playground
  cluster:
    certificate-authority: ca.crt
    server: https://my-kube-playground:6443

contexts:
- name: my-kube-admin@my-kube-playground
  context:
    cluster: my-kube-playground
    user: my-kube-admin

users:
- name: my-kube-admin
  user: 
    client-certificate: admin.crt
    client-key: admin.key
```

```yaml
apiVersion: v1
kind: Config
current-context: dev-user@google    #---> default context
clusters:
- name: my-kube-playground          #---> values hidden
- name: development
- name: production
- name: google

contexts:
- name: my-kube-admin@my-kube-playground
- name: dev-user@google
- name: prod-user@production

users:
- name: my-kube-admin
- name: admin
- name: dev-user
- name: prod-user
```

## Kubectl Config

```yaml
$ kubectl config view       #---> Use default kubeconfig file from user's home directory
$ kubectl config view -kubeconfig=my-custom-config  #---> specify kubeconfig file option
```

How do you change context to use prod user to access the production cluster?
```yaml
$ kubectl config use-context prod-user@production
$ kubectl config -h     #---> available commands with kubeconfig
```

Can we configure a context to switch to a particualr namespace?
Yes, the context section in the kubeconfig file can take an additional field called namespace.

```yaml
apiVersion: v1
kind: Config
clusters:
- name: my-kube-playground
  cluster:
    certificate-authority: ca.crt
    server: https://my-kube-playground:6443

contexts:
- name: my-kube-admin@my-kube-playground
  context:
    cluster: my-kube-playground
    user: my-kube-admin
    namespace: finance

users:
- name: my-kube-admin
  user: 
    client-certificate: admin.crt
    client-key: admin.key
```