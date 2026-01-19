# Network Policies

Lets consider a basic example of a traffic flowing through a web app and database server. Three componenets exist
- Web server -> serving front-end to users  -> port 80
- App server -> Serving bacn-end APIs       -> port 5000
- Database server                           -> port 3306

Two types of traffic exist in this scenario
- Ingress
- Egress

For the Web server
- Receives ingress traffic on port 80 from the user -> Ingress
- Sends egress traffic to port 5000 to the App server -> Egress

For the App server
- Receives Ingress traffic on port 5000 from the Web server
- Egress traffic to port 3306 to the DB server  

For the DB server
- Recevies Ingress traffic on port 3306 from the App server 

## Network Security in K8S

One of the prerequisite for networking in k8s
- The pods should be able to communicate with each other without having to configure anything additional like routes.
    - To enable communication between the pods, service needs to be created

K8s is configured by default with an ***all allow rule*** that allows traffic from any pod to any other pods or services within the cluster.

What if we do not want the front end web server to be able to communicate with the database server directly?
- Implement a network policy to allow traffic to the DB server only from the App server
- Network policy is another object in kubernetes just like pod, deployment, replicasets


## Network Policy - Labels and Selectors
Use selectors to apply network policy to a pod.
Eg. Allow Ingress Traffic from App server Pod on port 3306

```yaml
labels:
    role: db
```

```yaml
podSelector:
    matchLabels:
        role: db
```

## Network Policy - Rules

Allow Ingress traffic from API Pod on port 3306 in DB pod
```yaml
policyTypes:
- Ingress
ingress:
- from:
    - podSelector:
        matchLabels:
            name: api-pod
    ports:
    - protocol: TCP
      port: 3306
```

## Network Policy
This would create a policy that would block all traffic to the DB pod, excpet for traffic from the App server pod

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
    name: db-policy
spec:
    podSelector:
        matchLabels:
            role: db
    policyTypes:
    - Ingress
    ingress:
    - from:
      - podSelector:
            matchLabels:
                name: api-pod
      ports:
      - protocol: TCP
        port: 3306
```
The current policy allows any pod in any namespace with matching labels to reach the DB server

We only want to allow the App server pod in the prod namespace to reach the DB pod
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
    name: db-policy
spec:
    podSelector:
        matchLabels:
            role: db
    policyTypes:
    - Ingress
    ingress:
    - from:
      - podSelector:
            matchLabels:
                name: api-pod   #---> if we dont use this every pod in the namespace selector will be able to access the DB pod
        namespaceSelector:      #---> To prevent pods from other namespaces accessing the DB server
            matchLabels:        #---> Without namespaceSelector, any pod from any namespace can access the DB server
                kubernetes.io/,etadata.name: prod
      ports:
      - protocol: TCP
        port: 3306
```

Now suppose there is a backup server outside of the k8s cluster, and we want this server to connect to the DB pod. 

A network policy can be configured to allow traffic from certain IP addresses.
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
    name: db-policy
spec:
    podSelector:
        matchLabels:
            role: db
    policyTypes:
    - Ingress
    ingress:
    - from:
      - podSelector:                                #---> Pod selector
            matchLabels:
                name: api-pod   
        namespaceSelector:                          #---> Namespace selector 
            matchLabels:        
                kubernetes.io/,etadata.name: prod
      - ipBlock:                                    #---> IpBlock selector
            cidr: 192.168.5.10/32   #---> IP block allows to specify a range of IP addresses from which trrafic can be allowed to hit the DB pod
      ports:
      - protocol: TCP
        port: 3306
```

Instead of the backup server initiating a backup, lets assume we have an agent running on the DB pod that pushes backup to the backup server. In that case, traffic is originating from the DB pod to an external backup server, For this we need to define egress rule.
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
    name: db-policy
spec:
    podSelector:
        matchLabels:
            role: db
    policyTypes:
    - Ingress
    - Egress
    ingress:
    - from:
      - podSelector:                                #---> Pod selector
            matchLabels:
                name: api-pod   
        namespaceSelector:                          #---> Namespace selector 
            matchLabels:        
                kubernetes.io/,etadata.name: prod
      ports:
      - protocol: TCP
        port: 3306
    egress:
    - to:
      - ipBlock:
            cidr: 192.168.5.10/32
      ports:
      - protocol: TCP
        port: 80
```
