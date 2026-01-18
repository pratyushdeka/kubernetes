# Readiness Probe

Readiness probes determine when a container is ready to accept traffic. This is useful when waiting for an application to perform time-consuming initial tasks that depend on its backing services. For example
- establish network connections
- loading files
- warming caches

Readinsess probes can also useful later in the container's lifecycle, for example, when recovering from temporary faults or overloads.

If the readiness probe returns a failed state, k8s removes the pod from all matching service endpoints.

Readiness probes run on the contianer during its whole lifecycle.
