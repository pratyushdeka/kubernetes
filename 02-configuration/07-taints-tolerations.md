# Taints and Tolerations
Taints and tolerations are used to det restrictions on what pods can be scheduled on a node.

Node affinty is a property of Pods that attracts then to a set of nodes. **Taints** are the opposite -- they allow **node** to repel a set of pods.

**Tolerations** are applied to **pods**. Tolerations allow the scheduler to schedule pods with matching taints. Tolerations allow scheduling but don't guarantee scheduling: the scheduler also evaluates other parameters as part of its function.

## Taints - Node

```yaml
$ kubectl taint nodes node-name key=value:taint-effect
$ kubectl taint nodes node1 app=myapp:NoSchedule
```

taint-effect: What happens to PODs that do not tolerate this taint?
- NoSchedule
- PreferNoSchedule
- NoExecute

## Tolerations - Pod

```yaml
$ kubectl taint nodes node1 app = blue : NoSchedule
```
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-k8s-dashboard
spec:
    containers:
        - name: my-k8s-dashboard
          image: my-k8s-dashboard
    tolerations:
        - key: "app"
          operator: "Equal"
          value: "blue"
          effect: "NoSchedule"
```

Taints and tolerations does not tell the pod to go to a certain node Instead, it tells the node to only accept pods with certain tolerations.

### One interesting fact

```yaml
$ kubectl describe node kubemaster | grep Taint
Output:
    Taints: node-role.kubernetes.io/master:NoSchedule
```
Why the kube-scheduler does not schedule any pods on the master node?

When the Kubernetes cluster is set up, a taint is set on the master node that automatically prevents any pods from being scheduled on the master/control node.

How many nodes exist on the k8s cluster?
$ kubectl get nodes

Describe nodes
$ kubectl describe node node01

Create a taint on node01 with key of spray, value of mortein and effect of NoSchedule

$ k taint nodes node01 spray=mortein:NoSchedule

Create another pod named bee with the nginx image, which has a toleration set to the taint mortein.

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: bee
  name: bee
spec:
  containers:
  - image: nginx
    name: bee
    resources: {}
  tolerations:
    - key: "spray"
      value: "mortein"
      operator: "Equal"
      effect: "NoSchedule"
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```