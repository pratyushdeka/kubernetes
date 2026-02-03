# Admission Controllers

We can't achieve few things with RBAC. For example
- Review the configuration file upon pod creation request, look at the image and deny request if the images comes from public repository. Only allow images from a internal registry
- Only allow images from a specific internal registry
- Never use the latest tag for any images
- If the container is running as root user, allow certain capabilities only
- Enforce metadata always contains labels

Admission controllers help us implement better security measures to enforce how a cluster is used. 

kubelet -> authentication -> authorization -> **admission controllers** -> create pod

There are number of admission controllers that come pre-built with K8S
- AlwaysPullImages
- DefaultStorageClass
- EventRateLimit
- NamespaceExists
- NamespaceAutoProvision

For example,
```yaml
$ kubectl run nginx --image nginx --namespace blue      #---> namepsace blue dont exist
                                                        #---> So this command will throw an error

```
The previous step will fail due to the **NamespaceExists** admission controller being enabled in Kubernetes. This controller rejects requests for namespaces that do not already exist. Therefore, to automatically create a namespace that does not exist, you should enable the **NamespaceAutoProvision** admission controller.

NamespaceExists and NamespaceAutoProvision admission controllers have been deprecated and are now succeeded by the NamespaceLifecycle admission controller.

The NamespaceLifecycle admission controller ensures that any requests made to a non-existent namespace are rejected, and it safeguards the default namespaces, including default, kube-system, and kube-public, from being deleted.

```yaml
$ kube-apiserver -h | grep enable-admission-plugins
$ kubectl exec kube-apiserver-controlplane -n kube-system -- kube-apiserver -h | grep enable-admission-plugins      #---> in case of kubeadm setup
```

Now that the DefaultStorageClass admission controller has been disabled, please revisit the original Persistent Volume Claim (PVC) named myclaim to observe the new behavior.

Delete the existing PVC and reapply it to observe the effects of disabling the default StorageClass.

Delete the existing PVC named myclaim:

```kubectl delete pvc myclaim```

Reapply the same manifest:

```kubectl apply -f myclaim.yaml```

Check the status of the PVC:

```kubectl get pvc myclaim```

Note: Disabling the DefaultStorageClass admission controller removes the automatic assignment of the default StorageClass. The StorageClass that was previously marked as default has already been patched.

Since the kube-apiserver is running as pod you can check the process to see enabled and disabled plugins.

```ps -ef | grep kube-apiserver | grep admission-plugins```

