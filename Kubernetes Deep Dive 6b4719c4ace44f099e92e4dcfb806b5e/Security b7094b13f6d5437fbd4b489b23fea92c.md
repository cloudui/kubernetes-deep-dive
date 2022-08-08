# Security

Everything is an API object.

![Untitled](Security%20b7094b13f6d5437fbd4b489b23fea92c/Untitled.png)

# Authentication

RBAC since 1.6

- Role-based access control

Only allow rules, no deny rules (like security group)

- All powerful accounts dictates roles for production (admin account, essentially)

![Untitled](Security%20b7094b13f6d5437fbd4b489b23fea92c/Untitled%201.png)

Kubernetes doesn’t manage users at all. You have to manage users externally

- IAM
- Active Directories

Service accounts are used and managed by clusters

- For control plan to use
- You can (should) manage them
- Every pod gets associated with service account

![Untitled](Security%20b7094b13f6d5437fbd4b489b23fea92c/Untitled%202.png)

# Authorization

![Untitled](Security%20b7094b13f6d5437fbd4b489b23fea92c/Untitled%203.png)

Change Kubernetes config file.

- Users can be authneticated but not authorized

Two objects to give permission to a user:

- Role
- Role Binding

## RBAC Rule Example

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
	name: acgrbac
	namespace: acg
rules:
- apiGroups: [""]
	resources: ["pods"]
	verbs: ["get", "list", "watch"]
```

## RBAC RoleBinding

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
	name: acgrbac
	namespace: acg
subjects:
	kind: User
	name: nigel@acg.com
	apiGroup: ""
roleRef:
	kind: Role
	name: acgrbac
	apiGroup: ""
```

## Cluster Roles and RoleBindings

Not limited by namespace.

Can modify the whole cluster with permissions.

Define Roles as ClusterRoles and use namespace bindings

# Admission Control

![Screen Shot 2022-08-07 at 3.07.02 PM.png](Security%20b7094b13f6d5437fbd4b489b23fea92c/Screen_Shot_2022-08-07_at_3.07.02_PM.png)

Make sure policies and standards are enforced

Webhooks for extrenal admission controllers

- Hook to external systems which can do stuff for you
- Secure connection and make sure codebase is solid

![Untitled](Security%20b7094b13f6d5437fbd4b489b23fea92c/Untitled%204.png)

Mutating lets you modify requests and validating doesn’t

- All must allow request

Mutating admission webhooks are invoked first, and can modify objects sent to the API server to enforce custom defaults.

After all object modifications are complete, and after the incoming object is validated by the API server, validating admission webhooks are invoked and can reject requests to enforce custom policies.

[https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/](https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/)

Examples of mutating webhooks are, adding additional labels and annotations, injecting sidecar containers, etc.

Examples of validating webhooks allow access to only authorized namespaces, allowing/denying the incoming API requests based on corporate policy, etc

![Untitled](Security%20b7094b13f6d5437fbd4b489b23fea92c/Untitled%205.png)