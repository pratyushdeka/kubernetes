# Custom Resource Definition (CRD)

CRD object can be either namespaced or cluster scoped. 

Custom resource is an extension of the Kubernetes API that is not necessarily available in a default Kubernetes installation.

Q. We have provided an incomplete Custom Resource Definition (CRD) manifest located at /root/crd.yaml. Task is to complete this file to define a namespaced CRD named internals.datasets.kodekloud.com. Please ensure you adhere to the following specifications:
- The CRD must belong to the group datasets.kodekloud.com.
- The scope of the CRD should be set to Namespaced.
- The version must be v1, and it should be marked as both served: true and storage: true.

Additionally, include a basic OpenAPI v3 schema for the CRD under the spec section with the following fields:
- internalLoad (string)
- range (integer)
- percentage (string)
Once you have created the CRD, utilize the provided /root/custom.yaml file to create a corresponding custom resource.

```yaml
# crd.yaml
---
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: internals.datasets.kodekloud.com 
spec:
  group: datasets.kodekloud.com
  versions:
    - name: v1
      served: true
      storage: true
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                internalLoad:
                  type: string
                range:
                  type: integer
                percentage:
                  type: string
  scope: Namespaced 
  names:
    plural: internals
    singular: internal
    kind: Internal
    shortNames:
    - int
```

```yaml
# custom.yaml
---
kind: Internal
apiVersion: datasets.kodekloud.com/v1
metadata:
  name: internal-space
  namespace: default
spec:
  internalLoad: "high"
  range: 80
  percentage: "50"
```

```yaml
$ kubectl create -f crd.yaml
$ kubectl create -f custom.yaml
```

```yaml
# Describe CRD
$ kubectl describe crd crdname
```

Please create a custom resource instance named datacenter utilizing the existing Custom Resource Definition (CRD) with the following specifications:
- apiVersion: traffic.controller/v1
- kind: Global

Set the following fields under spec:
- dataField: 2
- access: true

```yaml
---
kind: Global
apiVersion: traffic.controller/v1
metadata:
  name: datacenter
  namespace: default
spec:
  dataField: 2
  access: true
```