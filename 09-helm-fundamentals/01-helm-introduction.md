# Introduction to Helm

Kubernetes is awesome at managing complex infrastructures. But deploying applications into kubernetes cluster can become very complicated.

A typical app is usually made up of a collection of objects that need to interconnect to make everything work. For example, even a relatively simple WordPress site might need the following
- A deploymnet to declare the pods that want to runlike MySQL DB server or the web servers
- A persistant volume to store the database
- A persistent volume claim
- A service to expose the web server running in a pod
- A secret to store the admin password
- And may be even more if you like periodic backups etc.

For every object, we might need separate YAML file and need to apply ***kubectl apply*** to every YAML file and vice-versa for upgrading, deleting the app.

Helm changes the paradigm. Helm works as package manager for Kubernetes with instal or uninstall wizard and also a release manager. It helps to treat Kubernetes apps as apps instead of just a collection of objects.

Kubernetes doesn't really care about our app as whole. All that it knows is that we declared various objects and it proceeds to make each of them exist in the cluster. It doesn't really know that the PV, PVC, the deployment, and secrets are all part of a Wordpress application.

Helm is built from the ground up to know about the staffs related to a application. Helm is alse called a package manager for Kubernetes. Helm looks at all of the objects as part of a group or package whenever we need to perform an action.

For example, a computer game is consist of hundred or thousands of files. These are executable files, audio files, game sounds, music, graphics, textures, images and many more. Imagine what would happen if we had to download these files separately and setup. Fortuantely we dont have to do that as we have the game installer, we run it, choose the directory and does the rest.

Helm helps you manage Kubernetes applications - Helm charts help you define, install, and upgrade event the most complex Kubernetes application. Charts are easy to create, version, share, and publish.

Features of Helm
- Manage complexity
    - Charts describe even the most complex apps, provide repeatable application installation, and server as a single point of authority
- Easy updates
    - Take the pain out of updates with in-place upgrades and custom hooks
- Simple Sharing
    - Charts are easy to version, share, and host on public or private servers
- Rollbacks
    - Use ***helm rollback*** to roll back to an older version of a release with ease

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

## Prerequisites
The following prerequisites are required for a successful and properly secured use of Helm. 
- A Kubernetes cluster
- Deciding what security configurations to apply to your installation, if any
- installing and configuring Helm

## Install Helm 
https://helm.sh/docs/intro/install

Need a Kubernetes cluster working with kubectl command configured

```
$ sudo snap install helm
```

## Three Big Concepts
### Chart
A Chart is a helm package, It contains all of the resources definitions necessary to run an application, tool, or service inside of a Kubernetes cluster. Think of it like the Kubernetes equivalent of Apt dpkg or Yum RPM file

### Repository
A Repository is the place where charts can be collected and shared. It's like Fedora Package database, but for Kubernetes packages. 

### Release
A Release is an instance of a chart running in a Kubernetes cluster. One chart can often be installed many times into the same cluster. And each time it is installed, a new release is created. Consider a MySQL chart. If you want two databases running in your cluster, you can install that chart twice. Each one will have its own release, which will in turn have its own release name.

Helm installs **charts** into Kuberntes, creating a new **release** for each installation. To find new charts, search helm chart **repositories**.

Q. Which command is used to find wordpress from Artifact hub?
```yaml
$ helm search hub wordpress
```
Q. Helm does not wait until all of the resources are running before it exits. Which command is used to check release state or re-read configuration information?
```yaml
$ helm status releaseName
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
