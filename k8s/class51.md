# Class 51 - Kubernetes RBAC
class url: https://youtu.be/XnRxnl0ITVg
## RBAC (Role Based Access Control)

RBAC is used to control **who can access Kubernetes resources and what actions they can perform**.

### RBAC Components

1. **Account**
2. **Role**
3. **RoleBinding** – attaches the Role to an Account

---

# Accounts

There are mainly two types of accounts:

### 1. User Account

* Used by **human users** to access Kubernetes.
* Example: Developer, Admin, DevOps Engineer.

### 2. Service Account

* Used by **applications/pods** that need access to the Kubernetes API.
* Service Accounts are commonly used by applications running inside Kubernetes.
* Authentication is generally done using **tokens**.

### Step 1: Create Service Account

Using command:

```bash
kubectl create sa k8s-sa
```

Or using YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: k8s-sa
```

Apply:

```bash
kubectl apply -f sa.yaml
```

### Step 2: Create Service Account Token

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: k8s-sa-token
  annotations:
    kubernetes.io/service-account.name: k8s-sa
type: kubernetes.io/service-account-token
```

> Modern Kubernetes versions commonly use short-lived projected ServiceAccount tokens automatically. The above Secret method is mainly useful when a long-lived token Secret is specifically required.

---

# Roles

A **Role** contains a set of rules that define what actions an account can perform on Kubernetes resources.

* Role is **namespace-scoped**.
* A Role works only inside the namespace where it is created.
* A Role must be attached to an account using a **RoleBinding**.

### Role API Version

```yaml
apiVersion: rbac.authorization.k8s.io/v1
```

### Common Role Fields

```yaml
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "create", "delete"]
```

### `apiGroups`

Defines which Kubernetes API groups the rule applies to.

Examples:

```yaml
apiGroups: [""]
```

Core resources such as:

* Pods
* Services
* ConfigMaps
* Secrets

```yaml
apiGroups: ["apps"]
```

Resources such as:

* Deployments
* ReplicaSets
* StatefulSets
* DaemonSets

```yaml
apiGroups: ["batch"]
```

Resources such as:

* Jobs
* CronJobs

```yaml
apiGroups: ["networking.k8s.io"]
```

Resources such as:

* Ingress
* NetworkPolicy

```yaml
apiGroups: ["rbac.authorization.k8s.io"]
```

Resources such as:

* Roles
* RoleBindings
* ClusterRoles
* ClusterRoleBindings

```yaml
apiGroups: ["storage.k8s.io"]
```

Resources such as:

* StorageClasses

```yaml
apiGroups: ["*"]
```

Applies to all API groups.

---

# Resources

`resources` specifies which Kubernetes objects the account can access.

Example:

```yaml
resources:
- pods
- services
- deployments
```

---

# ResourceNames

`resourceNames` can restrict access to specific resource names.

Example:

```yaml
resourceNames:
- my-pod
```

This allows the rule to apply specifically to `my-pod`.

---

# Verbs

Verbs define what operations the account can perform.

| Verb               | Purpose                             |
| ------------------ | ----------------------------------- |
| `get`              | Read a specific resource            |
| `list`             | List multiple resources             |
| `create`           | Create resources                    |
| `update`           | Update resources                    |
| `patch`            | Partially update resources          |
| `delete`           | Delete resources                    |
| `deletecollection` | Delete multiple resources           |
| `watch`            | Watch resource changes              |
| `bind`             | Bind roles                          |
| `approve`          | Approve certain Kubernetes requests |

### `get` vs `list`

```bash
kubectl get pod mypod
```

Uses:

```text
get
```

It accesses a specific Pod.

```bash
kubectl get pods
```

Uses:

```text
list
```

It lists multiple Pods.

> Note: `apply` and `post` are not normally RBAC verbs. `kubectl apply` generally uses API operations such as `create` and/or `patch`.

---

# Example Role

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: default

rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

This Role allows an account to:

* Get Pods
* List Pods
* Watch Pods

Only in the `default` namespace.

---

# RoleBinding

A **RoleBinding** attaches a Role to an account.

* RoleBinding is **namespace-scoped**.
* It connects:

  * **Subject** → Account/User/Group
  * **RoleRef** → Role or ClusterRole

### Example

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: default

subjects:
- kind: ServiceAccount
  name: k8s-sa
  namespace: default

roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### Flow

```text
ServiceAccount
      |
      | RoleBinding
      ↓
     Role
      |
      ↓
Kubernetes Resources
```

---

# Check Account Access

Use:

```bash
kubectl auth can-i <verb> <resource> --as=system:serviceaccount:<namespace>:<service-account>
```

Example:

```bash
kubectl auth can-i list pods \
--as=system:serviceaccount:default:k8s-sa
```

Example output:

```text
yes
```

---

# ClusterRole

A **ClusterRole** is used for cluster-wide permissions.

* It is **not namespace-scoped**.
* It can define permissions for resources across namespaces.
* It can also be used with namespace-scoped resources through a RoleBinding.

Example:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: pod-reader

rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

---

# ClusterRoleBinding

**ClusterRoleBinding** attaches a ClusterRole to an account for **cluster-wide access**.

Example:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: pod-reader-binding

subjects:
- kind: ServiceAccount
  name: k8s-sa
  namespace: default

roleRef:
  kind: ClusterRole
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

---

# Role vs ClusterRole

| Role                             | ClusterRole                       |
| -------------------------------- | --------------------------------- |
| Namespace-scoped                 | Cluster-wide definition           |
| Used for a specific namespace    | Can be used across namespaces     |
| Attached using RoleBinding       | Attached using ClusterRoleBinding |
| No cluster-wide access by itself | Can provide cluster-wide access   |

---

# RBAC Flow

```text
User / ServiceAccount
          |
          ↓
     RoleBinding
          |
          ↓
        Role
          |
          ↓
Kubernetes Resources
```

For cluster-wide access:

```text
User / ServiceAccount
          |
          ↓
ClusterRoleBinding
          |
          ↓
     ClusterRole
          |
          ↓
Cluster Resources
```

## Important Points

* **Account** → Who is accessing?
* **Role** → What permissions are given?
* **RoleBinding** → Who gets those permissions?
* **ClusterRole** → Cluster-level permission definition.
* **ClusterRoleBinding** → Gives ClusterRole permissions cluster-wide.
* RBAC controls **authorization**, not authentication.
