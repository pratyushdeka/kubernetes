# Patches

- Kustomize patches provide another method to midfying Kubernetes configs
- Unlike common transformers, patches provide a more "surgical" approach to targeting oneo r more specific sections ina a Kunernetes resource
- To create a patch 3 parameters must be provided:
    - Operation Type: Add/remove/replace
    - Target: What resoruce should this patch be applied on
        - Kind
        - Version/Group
        - Name
        - Namespace
        - labelSelector
        - AnnotationSelector
    - Value: What is the value that will either be replaced or added with (only needed for add/replace operations)

api-deployment.yaml
```yaml
aptVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment.  #---> replace with the below kustomization.yaml patching
spec:
    replicas: 1
    selector:
        matchLabels:
            component: api
    template:
        metadata:
            labels:
                component: api
        spec:
            containers:
            - name: nginx
              image: nginx
```
kustomization.yaml to replace the metadata name from api-deployment to web-deployment 
```yaml
patches:
    - target:
        kind: Deployment
        name: api-deployment
      patch: |-
        - op: replace
          path: /metadata/name
          value: web-deployement
```

kustomization.yaml to update the replicas from 1 to 5
```yaml
patches:
    - target:
        kind: Deployment
        name: api-deployment
    
      patch: |-
        - op: replace
          path: /spec/replicas
          value: 5
```

## There two ways of patching
- Json 6902 Patch
    - The ways mentioned above are the examples
- Strategic merge patch (aligned with k8s manifests)
    ```yaml
    patches:
        - patch: |-
            apiVersion: apps/v1
            kind: Deployment
            metadata:
                name: api-deployment
            spec:
                replicas: 5
    ```

## Different types of patching
### Json 6902 Patch Inline vs separate file

#### Inline
Kustomization.yaml
```yaml
patches:
    - target:
        kind: Deployment
        name: api-deployment
      patch: |-
        - op: replace
          path: /spec/replicas
          value: 5
```

#### Separate file
Kustomization.yaml
```yaml
patches:
    - path: replica-patch.yaml
      target:
        kind: Deployment
        name: api-deployment
```
replica-patch.yaml
```yaml
- op: replace
  path: /spec/replace
  value: 5
```

### Strategic merge patch Inline vs Separate File
#### Inline
```yaml
    patches:
        - patch: |-
            apiVersion: apps/v1
            kind: Deployment
            metadata:
                name: api-deployment
            spec:
                replicas: 5
```

#### Separate File
Kustomization.yaml
```yaml
patches:
    - replica-patch.yaml
```
replica-patch.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment
spec:
    replicas: 5
```

### Replace Dictionary Json6902
kustomization.yaml
```yaml
patches:
    - target:
        kind: Deployment
        name: api-deployment
      patch: |-
        - op: replace
          path: /spec/template/metadata/labels/component
          value: web
```

### Replace Dictionary Strategic Merge Patch
Kustomization.yaml
```yaml
patches:
    - label-patch.yaml
```
label-patch.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment
spec:
    template:
        metadata:
            labels:
                component: web
```

### Add Dictionary Json6902
kustomization.yaml
```yaml
patches:
    - target:
        kind: Deployment
        name: api-deployment
      patch: |-
        - op: add
          path: /spec/template/metadata/labels/org
          value: KodeKloud
```
Result: api-deployment.yaml
```yaml
aptVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment
spec:
    replicas: 1
    selector:
        matchLabels:
            component: api
    template:
        metadata:
            labels:
                component: api
                org: KodeKloud
        spec:
            containers:
            - name: nginx
              image: nginx
```

### Add Dictionary Strategic Merge Patch
Kustomization.yaml
```yaml
patches:
    - label-patch.yaml
```
label-patch.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment
spec:
    template:
        metadata:
            labels:
                org: KodeKloud
```

### Remove Dictionary Json6902
api-deployment.yaml
```yaml
aptVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment
spec:
    replicas: 1
    selector:
        matchLabels:
            component: api
    template:
        metadata:
            labels:
                component: api
                org: KodeKloud      #---> Remove org
        spec:
            containers:
            - name: nginx
              image: nginx
```
kustomization.yaml
```yaml
patches:
    - target:
        kind: Deployment
        name: api-deployment
      patch: |-
        - op: remove
          path: /spec/template/metadata/labels/org
```
Result: api-deployment.yaml
```yaml
aptVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment
spec:
    replicas: 1
    selector:
        matchLabels:
            component: api
    template:
        metadata:
            labels:
                component: api
        spec:
            containers:
            - name: nginx
              image: nginx
```

### Remove Dictionary Strategic Merge Patch
Kustomization.yaml
```yaml
patches:
    - label-patch.yaml
```
label-patch.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment
spec:
    template:
        metadata:
            labels:
                org: null
```

## Patches List
### Replace List Json6902
api-deployment.yaml
```yaml
aptVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment
spec:
    replicas: 1
    selector:
        matchLabels:
            component: api
    template:
        metadata:
            labels:
                component: api
                org: KodeKloud
        spec:
            containers:         #---> Replace the containers to the list
            - name: nginx
              image: nginx
```
kustomization.yaml
```yaml
patches:
    - target:
        kind: Deployment
        name: api-deployment
      patch: |-
        - op: replace
          path: /spec/template/spec/containers/0
          value:
            name: haproxy
            image: haproxy
```
Result ---> api-deployment.yaml
```yaml
aptVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment
spec:
    replicas: 1
    selector:
        matchLabels:
            component: api
    template:
        metadata:
            labels:
                component: api
                org: KodeKloud
        spec:
            containers:
            - name: haproxy
              image: haproxy
```

### Replace List Strategic Merge Patch
kustomization.yaml
```yaml
patches:
    - label-patch.yaml
```
label-patch.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment
spec:
    template:
        spec:
            containers:
                - name: nginx
                  image: haproxy        #---> update container image
```

### Add List Json6902
kustomization.yaml
```yaml
patches:
    - target:
        kind: Deployment
        name: api-deployment
      patch: |-
        - op: add
          path: /spec/template/spec/containers/-    #---> - means append tp the end of the list
          value:                                    #---> it can be any index
            name: haproxy
            image: haproxy
```
Result ---> api-deployment.yaml
```yaml
aptVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment
spec:
    replicas: 1
    selector:
        matchLabels:
            component: api
    template:
        metadata:
            labels:
                component: api
                org: KodeKloud
        spec:
            containers:     #---> Now we have two containers
            - name: nginx
              image: nginx
            - name: haproxy
              image: haproxy
```
### Add List Strategic Merge Patch
kustomization.yaml
```yaml
patches:
    - label-patch.yaml
```
label-patch.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment
spec:
    template:
        spec:
            containers:
                - name: haproxy
                  image: haproxy        #---> update container image
```

### Delete List Json6902
api-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment
spec:
    replicas: 1
    selector:
        matchLabels:
            component: api
    template:
        metadata:
            labels:
                component: api
                org: KodeKloud
        spec:
            containers:
            - name: nginx
              image: nginx
            - name: haproxy
              image: haproxy
```
Task: remove haproxy container
kustomization.yaml
```yaml
patches:
    - target:
        kind: Deployment
        name: api-deployment
      patch: |-
        - op: remove
          path: /spec/template/spec/containers/1    #---> index of container to delete
```
### Delete List Strategic Merge Patch
kustomization.yaml
```yaml
patches:
    - label-patch.yaml
```
label-patch.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment
spec:
    template:
        spec:
            containers:
                - $patch: delete    #---> Delete Directive specifies which container to delete
                  name: haproxy
```