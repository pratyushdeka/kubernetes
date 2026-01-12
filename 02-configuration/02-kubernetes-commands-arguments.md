# Commands and Arguments

From the previous ubuntu-sleeper container image, run
```yaml
$ docker run --name ubuntu-sleeper ubuntu-sleeper    ---> sleep 5
$ docker run --name ubuntu-sleeper ubuntu-sleeper 10 ---> sleep 10

```
Lets create a pod for this

***pod-definition.yaml***
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: ubuntu-sleeper
spec:
    containers:
        - name: ubuntu-sleeper
          image: ubuntu-sleeper
```

***pod-definition-args.yaml***
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: ubuntu-sleeper
spec:
    containers:
        - name: ubuntu-sleeper
          image: ubuntu-sleeper
          args: ["10"]
```
```
 $ kubectl create -f pod-definintion.yaml
```

***pod-definition-args-commands.yaml***
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: ubuntu-sleeper
spec:
    containers:
        - name: ubuntu-sleeper
          image: ubuntu-sleeper
          command: ["sleep2.0"]
          args: ["10"]
```
```
 $ kubectl create -f pod-definintion.yaml
```
To summarize there are two fields that correspond to two instructions in the dockerfile. The command field overrides the entrypoint instruction and the args field overrides the cmd instruction in the dockerfile. 
```
$ kubectl edit pod podName
$ kubectl replace --force -f pod-definiton.yaml
```