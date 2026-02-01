# API Groups

```yaml
$ curl https://kube-master:6443/version     #---> returns the version
$ curl https://kube-master:6443/api/v1/pods #---> list the pods
```

The k8s api is grouped into multiple groups based on their purposes. For eg.
- /api (core group like ns, po, rc, event, ep, nodes, bindings, pv, pvc, configmaps, secrets, svc)
- /apis (named group like /apps, /networking.k8s.io, /storage.k8s.io, etc.)
- /version (to view the verion of the cluster)
- /healthz (monitor the helath of the cluster)
- /metrics (monitor the helath of the cluster)
- /logs (integrating with third-party logging application)

Names Group
- /apis
    - /apps     #---> known as API Groups
        - /v1
            - /deployments      #---> known as Resources
                - list          #---> known as Verbs
                - get
                - create
                - delete
                - update
                - watch
            - /replicaset
            - /statefulsets
    - /netowrking.k8s.io
        - /v1
            - /networkpolicies
    - ... many more

To know inside the cluster
```yaml
$ curl https://localhost:6443 -k    #---> this might not be authenticated
$ curl https://localhost:6443 -k
        --key admin.key
        --cert admin.crt
        --cacert ca.crt
$ curl https://localhost:6443/apis -k | grep "name"

$ kubectl proxy
$ curl https://localhost:8001 -k
```