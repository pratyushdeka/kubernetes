# Authorization

Why Authorization?

As an admin we can get pod details, nodes, or delte node etc.
```yaml
$ k get pods
$ k get nodes
$ k delete node worker-2
``` 
But other users should not be able to perform all the admin related tasks.

Authorization mechanisms
- RBAC authorization (Role based access control)
- ABAC authorization (Attribute based access control)
- Node authorization
- Webhook mode

## ABAC
Associate a user or a group of users with a set of permissions.

