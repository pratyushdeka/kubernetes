# CKAD Certification Guide

Apart from the standard areas of pods and deployments, critical areas to focus on

- Docker Image Management
    - Practice building and tagging images in the specific format: hello:1.2.3
    - Familiarize with OCI image exports
    - Quick tip: Memorize the basic image tagging syntax as you'll need to be fast

- CronJobs Deep Dive
    - Study all possible CronJob configuration options
    - Key ares to master: Concurrency policies, History limits, Starting deadlines. Also, make sure you know where to find the Cron syntax in the docs, it's there

- Resource Management
    - Understand namespace-level resource limits
    - Practice setting and modifying: memory limits, CPU limits, resource quotas at namespace level

- Networking Troubleshooting
    
    You'll encounter complex scenarios involving services and ingress:
    - Practice debugging mismatched ports between Services and Ingress
    - Understanding traffic flow is crucial
    - Common issues include: Port misconfigurations, Service name mismatches

- Service Accounts and RBAC
    - Know how to identify and fix service account issues
    - Understand namespace context for service accounts

- secrets and ConfigMaps
    - Creating them, decoding them with base64, mounting them into container or as environment variables
    - Know how to mount secret as a file in the pod
    - Know how to inject keys from the secret as environment variables into your pod

## 10 Mistakes to avoid with
- Forgetting to set the context: There will be multiple namespaces and clusters on the exam day

    ```yaml
    $ kubectl config view
    $ kubectl config use-context k8s
    ```

- Not Copy/Paste

- Not knowing basic Linux shell tools
    - For example check the logs of a given pod and count Access Failed
    ```yaml
    $ kubectl logs access-log-generator | grep "Access Failed" > /tmp/access_failed.txt
    ```

- Not understanding what the task is asking
    - Task: Modify the pod flaky-web so that:
        - Every 10 seconds, the contianer is checked by making an HTTP GET request to http://localhost:8080/healthz
        - If the endpoint does not return a successful response within 2 seconds, the container should be considered unhealthy
        - After 3 consecutive failures, the container must be restarted automatically by Kubernetes
    - Solution: **Liveness probe**

- Struggling with text editors
    - In vim editor: set paste command. Run :set paste in command mode

- Using the wrong tool to troubleshoot
    - kubectl describe pod abcd (check events)
    - kubectl get events -n upsetti-spaghetti
    - kubectl logs nginx-busted

- Don't write manifests from scratch
    - copy k8s manifests from documentation
    - Use imperative commnds wherever possible
        - kubectl run -> create a pod with no manifest
            - kubectl run demo-pod --image=nginx
        - kubectl create can make all sorts of resources with no manifests
            - kubectl create deployment demo-deploy --image=nginx
        - kubectl expose can create services without a manifest
            - kubectl expose deployment demo-deploy --port=80

- Always check your work (double/triple check)
    - Task: Identify the pod consuming the most CPU across all namespaces in the cluster. Once identified, write the full name of this pod (including namepsace) to the file /tmp/hog_process

- Skip any task that is taking time or dont know have any clue

