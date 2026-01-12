# Enviroment Variables

Specify an environment variable in pod-definition.yaml 
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: webapp
spec:
    containers:
        - name: webapp
          image: webapp
          ports:
            - containerPort: 8080
          env:                      #---> An array/list of envrionment variables
            - name: APP_COLOR1      #---> Plain key value
              value: pink
            - name: APP_COLOR2      #---> ConfigMap
              valueFrom:
                configMapKeyRef:
            - name: APP_COLOR3      #---> Secrets
              valueFrom:
                secretKeyRef:
```

## ConfigMaps

Lets consider the below pod-definition file
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: webapp
spec:
    containers:
        - name: webapp
          image: webapp
          ports:
            - containerPort: 8080
          env:
            - name: APP_COLOR
              value: blue
            - name: APP_MODE
              value: prod
```

There are two steps configuring ConfigMaps
1. Create a ConfigMap named app-config

```yaml
APP_COLOR: blue
APP_MODE: prod
```

2. Inject into Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: webapp
spec:
    containers:
        - name: webapp
          image: webapp
          ports:
            - containerPort: 8080
          envFrom:
          - configMapRef:
              name: app-config
```

## Create a ConfigMap
There are two ways

1. Imperative
```
 $ kubectl create configmap <config-name> --from-literal=<key>=<value>    ---> syntax
 $ kubectl create configmap app-config --from-literal=APP_COLOR=blue \
                                       --from-literal=APP_MODE=prod
 $ kubectl create configmap <config-name> --from-file=<path-to-file>      ---> another way
 $ kubectl create configmap app-config --from-file=app_config.properties
```
Q: Create a new ConfigMap for the webapp-color POD. Use the spec given below
ConfigMap Name: webapp-config-map
Data: APP_COLOR=darkblue
Data: APP_OTHER=disregard
```
$ k create configmap webapp-config-map --from-literal=APP_COLOR=darkblue --from-literal=APP_OTHER=disregard
```

2. Declarative

Create a app-config.yaml
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
    name: app-config
data:
    APP_COLOR: blue
    APP_MODE: prod
```
Run the below command to create the ConfigMap app-config
```yaml
 $ kubectl create -f config-map.yaml
```

Similarly, we can have many examples of ConfigMap

Create a mysql-config.yaml
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
    name: mysql-config
data:
    port: 3306
    max_allowed_packet: 128M
```

Create a redis-config.yaml
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
    name: redis-config
data:
    port: 6379
    rdb-compression: yes
```

Viewing and describe ConfigMaps
```
 $ kubectl get configmaps
 $ kubectl describe configmaps
```

## ConfigMaps in Pods

Considering ConfigMap app-config is created, it can be injected into pods in the way below

1. Full ENV
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: webapp
spec:
    containers:
        - name: webapp
          image: webapp
          ports:
            - containerPort: 8080
          envFrom:
          - configMapRef:
              name: app-config
```
2. Single ENV
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: webapp
spec:
    containers:
        - name: webapp
          image: webapp
          ports:
            - containerPort: 8080
          env:
          - name: APP_COLOR
            valueFrom:
                configMapKeyRef:
                    name: app-config
                    key: APP_COLOR
```
3. Volume
```yaml
volumes:
- name: app-config-volume
  configMap:
    name: app-config
```

## Kubernetes Secrets

Lets look at an python app app.py

```python
import os
from flask import Flask

app = Flask(__name__)
@app.route("/")
def main():
    mysql.connector.connect(host='mysql', database='mysql', user='root', password='passwd')
    return render_template('hello.html', color=fetchcolor())

if __name__ = "__main__":
    app.run(host="0.0.0.0", port="8080")
```

Passing the host, user and the password as hardcoded is not an good idea.

## Secrets

```yaml
DB_Host: mysql
DB_User: root
DB_Password: passwd
```
There are two steps involved in working with secrets
1. Create secrets

There are two ways to create secrets
1. Imperative
```
$ kubectl create secret generic <secrest-name> --from-literal=<key>=<value>
Eg.
$ kubectl create secret generic app-secret --from-literal=DB_Host=mysql \
                                           --from-literal=DB_User=root  \
                                           --from-literal=DB_Password=passwd
Another way to use files
$ kubectl create secret generic <secrest-name> --from-file=app_secret.properties
Eg.
$ kubectl create secret generic app-secret --from-file=app_secret.properties

```
2. Declarative
Create a definition file secret-data.yaml
```yaml
apiVersion: v1
kind: Secret
metadata:
    name: app-secret
data:
    DB_Host: mysql
    DB_User: root
    DB_Password: passwd
```
Create the secret
```
$ kubectl create -f secret-data.yaml
```
Here we have specified the data in plain text, which is not very safe. So while creating a secret with a declarative approach, you must specify the secret values in a hashed format. How do we convert the plaintext to hashed format
```yaml
DB_Host: mysql
DB_User: root
DB_Password: passwd
```
to 
```yaml
DB_Host: bXlzcWw=             #---> echo -n 'mysql' | base64
DB_User: cm9vdA==             #---> echo -n 'root' | base64
DB_Password: cGFzc3dk         #---> echo -n 'passwd' | base64
```

To view and describe the secrets, run
```
$ kubectl get secrets
$ kubectl describe secrets
$ kubectl get secret app-secret -o yaml
```
How to decode secrets
```yaml
DB_Host: bXlzcWw=             #---> echo -n 'mysql' | base64 --decode
DB_User: cm9vdA==             #---> echo -n 'root' | base64 --decode
DB_Password: cGFzc3dk         #---> echo -n 'passwd' | base64 --decode
```


2. Inject into Pod / Secrets in Pods

secret-data.yaml
```yaml
apiVersion: v1
kind: Secret
metadata:
    name: app-secret
data:
    DB_Host: bXlzcWw=
    DB_User: cm9vdA==
    DB_Password: cGFzc3dk
```

pod-definition.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: webapp
spec:
    containers:
        - name: webapp
          image: webapp
          ports:
            - containerPort: 8080
          envFrom:
          - secretRef:
              name: app-secret
```

Then run 
```
$ kubectl create -f pod-definition.yaml
```
## Secrets in Pods

Considering secret app-secret is created, it can be injected into pods in the way below

1. Full ENV
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: webapp
spec:
    containers:
        - name: webapp
          image: webapp
          ports:
            - containerPort: 8080
          envFrom:
          - secretRef:
              name: app-secret
```
2. Single ENV
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: webapp
spec:
    containers:
        - name: webapp
          image: webapp
          ports:
            - containerPort: 8080
          env:
          - name: DB_Password
            valueFrom:
                secretKeyRef:
                    name: app-secret
                    key: DB_Password
```
3. Volume
```yaml
volumes:
- name: app-secret-volume
  secret:
    name: app-secret
```
If you were to mount the secret as a volume in the pod, each attribute in the secret is created as a file with the value of the secret as its content. In this case, since we have three attributes in our secret, three files are created, and if we look at the contents of the DB_Password file, we see the password in it.
```
$ ls /opt/app-secret-volumes
DB_Host     DB_Password        DB_User

$ cat /opt/app-secret-volumes/DB_Password
passwd
```