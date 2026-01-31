# Transformers
Kustomize transformers let us modify Kubernetes configs.

## Common Transformers

Common transformers allow us to make common configuration changes across all of our Kubernetes resources.

- commonLabel
    - adds a label to all k8s resources
- namePrefix/Suffix
    - adds a common prefix/suffix to all resource names
- Namespace
    - adds a common namespace to all resources
- commonAnnotations
    - adds an annotation to all resources