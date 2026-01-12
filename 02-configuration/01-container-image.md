# Container Images

### How to create my own image?

Flask app dockerfile
```
FROM Ubuntu

RUN apt-get update
RUN apt-get install python

RUN pip install flask
RUN pip install flask-mysql

COPY . /opt/src

ENTRYPOINT FLASK_APP=/opt/src/app.py flask run
----------------------------------------------

$ docker build Dockerfile -t pratyushdeka/flask-app
$ docker push pratyushdeka/flask-app -------------------> Registry

```

```
Dockerfile Content

INSTRUCTION ARGUMENT
```

FROM, RUN, COPY, ENTRYPOINT are instructions, rest all are arguments line by line

## Commands, Arguments and Entrypoints in Docker

Unlike VM's Container are not meant to host an OS. Containers are meant to run a specific task or process, such as to host an instance of a web server or application server or a database, or simple to carry out some kind of computation or analysis. Once the task is complete, the container exits. 

A container only lives as long as the process inside it is alive.

```
 $ docker run ubuntu ---> It runs an ubuntu image as container and exits immediately
 $ docker ps         ---> will not list any container images running
 $ docker ps -a      ---> will list the ubuntu container ran and stopped and in exited status
```

### Who defines what process runs within the container?
Looking at the Dockerfile of nginx, we will see an instruction called CMD which stands for command that defines the program that will be run within the container when it starts.

For nginx image, it is nginx commnds. ---> **CMD["nginx"]**

For MySQL image it is the mysqld command.   ---> **CMD["mysqld"]**

For the ubuntu image it is the bash command.  ---> **CMD["bash"]**

When we ran the ubuntu container earlier, Docker created a container from the ubuntu image and launched the bash program. By default, Docker does not attach a terminal to a container when it is run, and so the bash program does not find the terminal and so it exits.

### So how do you specify a different command to start the container?
Option 1 - Append a command to the docker run command, it will override the default

```
 $ docker run ubuntu [COMMAND]
 $ docker run ubuntu sleep 5
```
If we want the image to always run the sleep command when it starts, we would have to create own image from the base ubuntu image and specify a new command
```
Dockerfile

FROM Ubuntu
CMD sleep 5
```

CMD Format
```
1. Shell form        -> CMD command param1       ------> CMD sleep 5
2. JSON array format -> CMD ["command","param1"] ------> CMD ["sleep","5"]
In JSON format, the first element should be executable
```

Build the docker image now and run
```
 $ docker build -t ubuntu-sleeper .
 $ docker run ubuntu-sleeper.  ---> it will sleep for 5 seconds and exits
 $ docker run ubuntu-sleeper sleep 10 ---> overriding the argument to sleep 10 seconds. 
```

The name of the image, ubuntu Sleeper in itself implies that the container will sleep, so we shouldn't have to specify the sleep command again. Instead, we would like it to be something like this.

```
$ docker run ubuntu-sleeper 10
```
Command at start up: sleep 10
This can be done through **ENTRYPOINT** instruction.
```
FROM Ubuntu
ENTRYPOINT ["sleep"]
```
Then run the below commnd
```
$ docker run ubuntu-sleeper 10 ---> sleep 10 (command at startup, 10 is appended to the entrypoint command)
```

```
 $ docker run ubuntu-sleeper ---> error 
```
Command at startup: sleep ---> error

Entrypoint defines the executable command. CMD provides default args as in. You can specify the program that will be run when the container starts, and whatever you specify on the command line. In this case, ten will get appended to the entrypoint. So the command that will be run when the container starts is sleep ten. So that's the difference between the two. In case of CMD instruction the command line parameters passed will get replaced entirely, whereas in case of entrypoint the command line parameters will get appended.

### How do you configure a default value for the command if one was not specified in the command line

```
Dockerfile

FROM Ubuntu
ENTRYPOINT ["sleep"]
CMD ["5"]
```
```
 $ docker run ubuntu-sleeper ---> sleep 5 (command at startup)
 $ docker run ubuntu-sleeper 10 ---> sleep 10 (command at startup)
```

### Can we modify the **ENTRYPOINT** during runtime?

Override the docker run command using --entrypoint option
```
 $ docker run --entrypoint sleep2.0 ubuntu-sleeper 10
```

Deploy a redis pod using the redis:alpine image with the labels tier=db.
```
k run redis --image=redis:alpine -l tier=db
```

Create a service named redis-service to expose the existing redis pod within the cluster on port 6379
```
kubectl expose pod redis --port=6379 --name=redis-service --type=ClusterIP
```

Create a deployment named webapp using the image kodekloud/webapp-color with 3 replicas.
```
kubectl create deployment  webapp --image=kodekloud/webapp-color --replicas=3
```

```yaml
$ k create namespace dev-ns
$ k get all
$ k get namespace
```

Create a new deployment called redis-deploy in the dev-ns namespace with the redis image. It should have 2 replicas.
```
kubectl create deployment redis-deploy --image=redis --replicas=2 -n dev-ns
```

Create a pod named httpd using the image httpd:alpine in the default namespace.
Then, create a service of type ClusterIP with the same name (httpd) that exposes the pod on port 80.
```
k expose pod httpd --port=80 --name=httpd --type=ClusterIP
service/httpd exposed
```


```
```