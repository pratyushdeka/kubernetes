# Multi-container pods 

The idea of decoupling a large monolithic application into subcomponents known as microservices enable us to develop and deploy a set of independent, small and reusable code.

Microservices architecture helps us to scale up and down, as well as modify the services as required. At times, we may need two services to work together such as a web server and the main app. That is why we have muti-continaer pods that share same lifecycle (created adn destroyed together), share same network space and have same storage volumes.

## Multi-container Pods design Patterns

- Co-located Containers (multiple containers running continuously throughout the pod lifecycle)
- Regular Init Containers (There is an init container which does its job before starting the main container)
- Sidecar Containers ()

Co-located Containers
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: simple-webapp
    labels:
        name: simple-webapp
spec:
    containers:
        - name: webapp
          image: webapp
          ports:
            - containerPort: 8080
        - name: main-app
          image: main-app
```
Regular Init Containers
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: simple-webapp
    labels:
        name: simple-webapp
spec:
    containers:
        - name: webapp
          image: webapp
          ports:
            - containerPort: 8080
        initContainers:
        - name: db-checker
          image: busybox
          command: 'wait-for-db-to-start.sh'
```
There can be multiple init containers
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: simple-webapp
    labels:
        name: simple-webapp
spec:
    containers:
        - name: webapp
          image: webapp
          ports:
            - containerPort: 8080
        initContainers:
        - name: db-checker
          image: busybox
          command: 'wait-for-db-to-start.sh'
        - name: api-checker
          image: busybox
          command: 'wait-for-another-api.sh'
```

Sidecar Containers
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: simple-webapp
    labels:
        name: simple-webapp
spec:
    containers:
        - name: webapp
          image: webapp
          ports:
            - containerPort: 8080
        initContainers:
        - name: log-shipper
          image: busybox
          command: 'setup-log-shipper.sh'
          restartPolicy: Always
```
For example, ELK stack. ElasticSearch captures the logs and Kibana is the visualiser. 