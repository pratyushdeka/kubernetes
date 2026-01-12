# Node Selectors

Node selector pod definition example
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: myapp-pod
spec:
    containers:
        - name: data-processor
          image: data-processor
    nodeSelector:
        size: Large
```

## Label Nodes

```yaml
$ kubectl label nodes <node-name> <label-key>=<label-value>
$ kubectl label nodes node-1 size=large 
```

## Node Selectors - Limitations
Canot implement like below
- Large or medium?
- NOT Small


# Node Affinity

Node affinity like Large or medium?
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: myapp-pod
spec:
    containers:
        - name: data-processor
          image: data-processor
    affinity:
        nodeAffinity:
            requiredDuringSchedulingIgnoreDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                    - key: size
                      operator: In
                      values:
                      - Large
                      - Medium
```

Node affinity like NOT Small
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: myapp-pod
spec:
    containers:
        - name: data-processor
          image: data-processor
    affinity:
        nodeAffinity:
            requiredDuringSchedulingIgnoreDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                    - key: size
                      operator: NotIn
                      values:
                      - Small
```

## Node Affinity Types
The type of node affinity defines the behavior of the scheduler with respect to node affinity, and the stages in the lifecycle of the pod.

- Available
    - requiredDuringSchedulingIgnoreDuringExecution
    - preferredDuringSchedulingIgnoreDuringExecution
- Planned
    - requiredDuringSchedulingRequiredDuringExecution


Example: deployment definition
```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: red
spec:
  replicas: 2
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: node-role.kubernetes.io/control-plane
                operator: Exists
```