# Resource Requirements

Suppose we have three node k8s cluster. Each node has a set of CPU and memory resources available. Now every pod requires a set of resoruces to run. 

It is the ***kube-scheduler*** that decides which node a pod goes to. The scheduler takes into consideration the amount of resources required by a pod and those available on the nodes, and identifies the best node to place a pod on.

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: webapp
spec:
    containers:
        - name: webapp
          image: webapp
          ports:
            - containerPort: 8080
          resources:
            requests:
                memory: "4Gi"
                cpu: 2
```

So when the scheduler gets a request to place this pod, it looks for a node that has this amount of resources available. And when the pod gets placed on a node, the pod gets a guaranteed amount of resources available for it.

### Resource - CPU

1 CPU - 1 AWS vCPU \
        1 GCP Core \
        1 Azure Core \
        1 Hyperthread \

### Resource - Memory
1 G (Gigabyte) = 1,000,000,000 bytes
1 M (Megabyte) = 1,000,000 bytes
1 K (Kilobyte) = 1,000 bytes

1 Gi (Gibibyte) = 1,073,741,824 bytes
1 Mi (Mebibyte) = 1,048,576 bytes
1 Ki (Kibibyte) = 1,024 bytes

## Resource Limits
By default Kubernetes does not have a CPU or memory request or limit set.

So by default, container running on a node has no limit to the resources it can consume on that node. It could go up and consume as much resources it requires and may suffocates the native processes on the node or the other containers of resources. We can set a limit for the resources usage on these pods.

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: webapp
spec:
    containers:
        - name: webapp
          image: webapp
          ports:
            - containerPort: 8080
          resources:
            requests:
                memory: "1Gi"
                cpu: 2
            limits:
                memory: "2Gi"
                cpu: 2
```

### Exceed Limits
What happens when a pod tries to exceed resources beyond its specified limits?

In case of CPU, the system throttles the CPU so that it does go beyond the specified limit. A container cannot use more CPU resources than its limit.

In case of Memory, A container can use more memory resources than its limit, so if a pod tries to consume more memory than its limit constantly. The pod will be terminated and you'll see that the pod terminated with an error in the logs or in the output of the describe command when you run it. So that's what is OOM refers to Out of Memory (OOM) kill.


### Limit Ranges

How do we ensure that every pod created has some defaults?

***LimitRange*** can help to define default values to be set for containers in pods that are created without a request or limit specidifed in the pod definition files. This is applicable at the namespace level.

limit-range-cpu.yaml
```yaml
apiVersion: v1
kind: LimitRange
metadata:
    name: cpu-resource-constraint
spec:
    limits:
    - default:                  #---> Limit
        cpu: 500m
      defaultRequest:           #---> Request
        cpu: 500m
      max:                      #---> Limit
        cpu: "1"
      min:                      #---> Request
        cpu: 100m
      type: Container
```

limit-range-memory.yaml
```yaml
apiVersion: v1
kind: LimitRange
metadata:
    name: memory-resource-constraint
spec:
    limits:
    - default:                  #---> Limit
        memory: 1Gi
      defaultRequest:           #---> Request
        cpu: 1Gi
      max:                      #---> Limit
        cpu: 1Gi
      min:                      #---> Request
        cpu: 500Mi
      type: Container
```

## Resource Quotas

Is there any way to restrict the total amount of resources that can be consumed by applications deployed in a Kubernetes cluster?

For example, if we had to say that all the pods together shouldn't consume more than this much of CPU or memory.

A resource quota is a namespace level object that can be created to set hard limits for requests and limits.

resource-quota.yaml
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
    name: resource-quota
spec:
    hard:
        requests.cpu: 4
        requests.memory: 4Gi
        limits.apu: 10
        limits.memory: 10Gi
```