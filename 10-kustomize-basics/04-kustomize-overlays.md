# Overlays
Kustomize has the concepts of bases and overlays. Kustomize allow us us to take a base k8s config and kustomize it on a per environment basis. If we have multiple environments like dev, stg and prod, we can tweak properties ona a per environment basis.

- k8s
    - base/              #---> share or default configs across all environments 
        - kustomization.yaml
        - nginx-deployment.yaml
        - service.yaml
        - redis-deployment.yaml
    - overlays/          #---> Environment specific configurations that add or modify base configs
        - dev/
            - kustomization.yaml
            - config-map.yaml
        - stg/
            - kustomization.yaml
            - config-map.yaml
        - prod/
            - kustomization.yaml
            - config-map.yaml

base/kustomization.yaml
```yaml
resources:
    - nginx-deployment.yaml
    - service.yaml
    - redis-deployment.yaml
```

base/nginx-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: nginx-deployment
spec:
    replicas: 1
    selector:
        matchLabels:
            component: nginx
    template:
        metadata:
            labels:
                component: nginx
        spec:
            containers:
            - name: nginx
              image: nginx
```
dev/kustomization.yaml
```yaml
bases:
    - ../../base        #---> relative path to kustomization.yaml file in base folder
patch: |-
    - op: replace
      path: /spec/replicas
      value: 2
```
prod/kustomization.yaml
```yaml
bases:
    - ../../base
patch: |-
    - op: replace
      path: /spec/replicas
      value: 3
```

We can also extend overlays other than patching. In one of the env configs, we can have new configs. For example
- k8s
    - base/
        - kustomization.yaml
        - nginx-deployment.yaml
        - service.yaml
        - redis-deployment.yaml
    - overlays/
        - dev/
            - kustomization.yaml
            - config-map.yaml
        - stg/
            - kustomization.yaml
            - config-map.yaml
        - prod/
            - kustomization.yaml
            - config-map.yaml
            - grafana-deployment.yaml #---> new resource added

prod/kustomization.yaml
```yaml
bases:
    - ../../base

resources:
    - grafana-deployement.yaml

patch: |-
    - op: replace
      path: /spec/replicas
      value: 3
```