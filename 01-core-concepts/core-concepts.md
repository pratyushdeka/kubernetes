
# Core Concepts

## Pods
How many **pods** exist on the system? In the current (default) namespace.

``
  $ kubectl get pods
``

Create a new pod using the nginx image.

```
 $ kubectl run --help ( To about the syntax)
 $ kubectl run nginx --image=nginx
 $ kubectl get pods
```
Which image is specified for the pods whose names begin with the newpods- prefix? 
Which nodes are these pods placed on?

You must look at one of the new pods in detail to figure this out.

```
 $ kubectl describe pod podName
```

Which nodes are these pods placed on?
```
 $ kubectl get pods -o wide
```

We just created a new pod named webapp. How many containers are part of the webapp pod?
```
 $ kubectl get pods 
```
What images are used in the **webapp** pod?
- Look at all the contianers in the pod 
 
```
 $ kubectl describe pod webapp
```

Why do you think the container agentx in pod webapp is in error?
 - Inspect the events of the pod to determine the reason for the container's failure to start.

State is waiting and the reason is **ImagePullBackOff** -> Failed to pull image "agentx": failed to pull and unpack image "docker.io/library/agentx:latest": failed to resolve reference "docker.io/library/agentx:latest": pull access denied, repository does not exist or may require authorization: server message: insufficient_scope: authorization failed

What does the READY column in the output of the kubectl get pods command indicate?
- Ready containers in the pod / Total containers in the pod

Delete the webapp Pod.
```
 $ kubectl delete pod webapp
```

Create a new pod with the name redis and the image redis123.
Utilize a pod-definition YAML file. Please note that the image name redis123 is intentionally incorrect. Do NOT correct the image at this stage; you will address this in the subsequent task.

You can get the definition using the below command
```
 $ kubectl run redis --image=redis123 --dry-run -o yaml > redis.yaml
 $ kubectl create -f redis.yaml
```
```redis.yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: redis
  name: redis
spec:
  containers:
  - image: redis123
    name: redis
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
Change the image to redis on this pod to redis.
- Update the redis.yaml file and run the below command

```
 $ kubectl apply -f redis.yaml
```

### Edit pods

To edit an existing pod, do the following
- If pod definition file given, edit the file and use it to create the pod
- If pod definition file not given, then you may extract the difinition to a file using the below command. Then edit the file to make necessary changes, delete, and re-create the pod
```
$ kubectl get pod <podName> -o yaml > pod-definition.yaml
```
- To modify the properties of the pod, utilize **$ kubectl edit pod podName**. Not all the properties are editable (check documentation)

## ReplicaSets

### Replicaset
A ReplicaSet's purpose is to maintain a stable set of replica Pods running at any given time. As such, it is often used to guarantee the availability of a specified number of identical Pods.
- Highly available
- Load balancing and scaling

ReplicaSet ensures desired number of pods always run.
```
 $ kubectl explain replicaset
```
```
apiVersion: apps/v1
kind: ReplicaSet
metadata: -------------------------- Replicaset
    name: myapp-replicaset
    labels:
        app: myapp
        type: front-end
spec: ------------------------------ Replicaset
    template:
        metadata: ------------------ Pod
            name: myapp-pod
            labels:
                app: myapp
                type: front-end
        spec: ---------------------- Pod
            containers:
            - name: nginx-container
              image: nginx
    replicas: 3
    selector: 
        matchLabels:
            type: front-end
```
```
To get create the ReplicaSet
 $ kubectl create -f rs-definition.yaml

To get the details of ReplicaSet created
 $ kubectl get replicaset

To get the pods created as part of the ReplicaSet
 $ kubectl get pods
```

How to scale the ReplicaSet
- Update the ReplicaSet definition and apply
```
 $ kubectl apply -f rs-definition.yaml
```
- Use kubectl scale
```
 $ kubectl scale --replicas=6 -f rs-definition.yaml
 OR
 $ kubectl scale --replicas=6 replicaset myapp-replicaset
```
Edit the ReplicaSet
```
 $ kubectl edit replicaset myapp-replicaset
``` 

Delete the ReplicaSet 
```
 $ kubectl delete replicaset myapp-replicaset
``` 
