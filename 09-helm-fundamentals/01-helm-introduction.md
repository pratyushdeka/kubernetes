# Introduction to Helm

Kubernetes is awesome at managing complex infrastructures. Deploying applications into kubernetes cluster can become very complicated.

A typical app isually made up of a collection of objects that need to interconnect to make everything work. For example, even a relatively simple WordPress site might need the following
- A deploymnet to declare the pods that want to runlike MySQL DB server or the web servers
- A persistant volume to store the database
- A persistent volume claim
- A service to expose the web server running in a pod
- A secret to store the admin password
- And may be even more if you like periodic backups etc.

For every object, we might need separate YAML file and need to apply ***kubectl apply*** to every YAML file. And vice-versa for deleting the app.

Helm changes the paradigm. Helm works as packaage manager for Kubernetes with instal or uninstall wizard and also a release manager. It helps to treat Kubernetes apps as apps instead of just a collection of objects.

Kubernetes doesn't really care about our app as whole. All that it knows is that we declared various objects and it proceeds to make each of them exist in the cluster. It doesn't really know that the PV, PVC, the deployment, and secrets are all part of a Wordpress application.

Helm is built from the ground up to know about the staffs related to a application. Helm is alse called a package manager for Kubernetes. Helm looks at all of the objects as part of a group or package whenever we need to perform an action.

For example, a computer game is consist of hundred or thousands of files. These are executable files, audio files, game sounds, music, graphics, textures, images and many more. Imagine what would happen if we had to download these files separately and setup. Fortuantely we dont have to do that as we have the game installer, we run it, choose the directory and does the rest.

Helm does the same thing for the YAML files and the Kubernetes objects that make our application. The advantages are
- Single command to install the application. Helm proceeds to automatically add every necessary object to Kubernetes without bothering us with the details.
```yaml
$ helm install wordpress
```
- Customize the settings for the package or the application by specifying desrired values at install time. Instead of having to edit multiple files in multiple YAML files, we can declare a a custom setting like values.yaml
    - can change the persistant volume size
    - choose the name of the website
    - update admin password 
- Upgrade application. Helm will what individual objects need to change to make this happen
```yaml
$ helm upgrade wordpress
```
- Rollback to the previous version
```yaml
$ helm rollback wordpress
```
- Uninstall the application
```yaml
$ helm uninstall wordpress
```

## Install Helm 
https://helm.sh/docs/intro/install

Need a Kubernetes cluster working with kubectl command configured

```
$ sudo snap install helm
```

Q. Which command is used to find wordpress from Artifact hub?
```yaml
$ helm search hub wordpress
```

Q. Add a bitnami helm chart repository in the controlplane node. 
- name - bitnami
- chart repo name - https://charts.bitnami.com/bitnami

```yaml
$ helm repo add bitnami https://charts.bitnami.com/bitnami
```

Q. Which command is used to search for the joomla package from the added repository?

```yaml
$ helm search repo joomla
```
Q. What is the app version of joomla in the bitnami helm repository?
```yaml
$ helm search repo joomla
```
Q. How many repos are present in controlplane node
```yaml
$ helm repo list
```
Q. Install drupal helm chart from the bitnami repository.
- Release name should be bravo.
- Chart name should be bitnami/drupal.

Note: Ignore the state of the application now.
```yaml
$ helm install bravo bitnami/drupal
```
Q. Uninstall the drupal helm pakcage which we installed earlier.
```yaml
$ helm uninstall bravo
```
Q. Download the bitnami apache package under the /root directory with version 10.1.1
```yaml
$ helm pull --untar  bitnami/apache --version 10.1.1
```
