# Security Contexts in Kubernetes

Lets first discuss security context in Docker. The host and the Docker containers share the same kernel but containers have their own namespaces. All the processes run by the containers are in fact run on the host itself, but in their own namespace.

By default. Docker runs processes within containers as the root user. If we want the container to start with non-root user, then we can pass the user option
```
$ docker run --user=1001 ubuntu sleep 3600
``` 
Now the sleep process runs within the container with the user 1001

Another way to enforce user security is to have this defined in the Docker image itself at the time of creation.
```docker
FROM ubuntu
USER 1001
```

```
$ docker build -t my-ubuntu-image .
$ docker run my-ubuntu-image sleep 3600
```

### What happens when you run the container as the root user on the host?

Docker implements a set of security features that limits the abilities of the root user within the container. So the root user within the container isn't really like the root user on the host. 

By default, Docker runs a container with a limited set of capabilities. So the processes running within the container do not have the privilege to reboot the host or perform operations that can disrupt the host or other containers running on the same host.

## Container Security

We can define a set of security standards, such as the ID of the user used to run the caontainer, the Linux capabilities that can be added or removed from the container.
```docker
$ docker run --user=1001 ubuntu sleep 3600
$ docker run -cap-add MAC_ADMIN ubuntu
```

## Kubernetes Security

In k8s containers are encapsulated in pods. Security settings can be configured at pod level or container level. If pod level security settings configured, the settings will carry over to all the containers within the pod. If configured at the container level, then the settings at the pod level will be overridden.

Pod level security settings
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: web-pod
spec:
    securityContext:
        runAsUser: 1000
    container:
        - name: ubuntu
          image: ubuntu
          command: ["sleep", "3600"]
```

Container level security setting
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: web-pod
spec:
    container:
        - name: ubuntu
          image: ubuntu
          command: ["sleep", "3600"]
          securityContext:
            runAsUser: 1000
            capabilities: ["MAC_ADMIN"]
```
Note: Capabilities are onlysupported at the contianer level and not at the POD level