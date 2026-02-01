# Components

- Components provide the ability to define reusable peices of configuration logic (resources + patches) that can be included in multiple overlays
- Components are useful in situations where applications support multiple optional features that need to be enabled only in a subset of overlays 

- k8s
    - base/
        - kustomization.yaml
        - nginx-deployment.yaml
        - service.yaml
        - redis-deployment.yaml
    - **components**/
        - caching/
            - kustomization.yaml
            - deployment-patch.yaml
            - redis-depl.yaml
        - db/
            - kustomization.yaml
            - deployment-patch.yaml
            - postgres-depl.yaml
    - overlays/
        - dev/
            - kustomization.yaml
            - config-map.yaml
        - stg/
            - kustomization.yaml
            - config-map.yaml
        - premium/
            - kustomization.yaml
        - prod/
            - kustomization.yaml
            - config-map.yaml

components/db/kustomization.yaml
```yaml
apiVersion: kustomize.config.k8s.io/v1alpha1
kind: Component

resources:
    - postgres-depl.yaml

secretGenerator:
    - name: postgres-cred
      literals:
        - password=postgres123

patches:
    - deployment-patch.yaml
```

components/db/deployment-patch.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: api-deployment
spec:
    template:
        spec:
            containers:
                - name: api
                  env:
                    - name: DB_PASSWORD
                      valueFrom:
                        secretKeyRef:
                            name: postgres-cred
                            key: password
```

How to use component in overlays

overlays/premium/kustomization.yaml
```yaml
bases:
    - ../../base

components:
    - ../../components/db
```