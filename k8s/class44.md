# Class 44 – Kubernetes
class url: https://youtu.be/cEtCD6bJhzI
## 1. StatefulSet

A **StatefulSet** is used for applications that need a **stable identity and storage**.

### Examples

* MySQL
* MongoDB
* PostgreSQL
* Kafka

### Pod Names

StatefulSet creates fixed Pod names:

```text
mysql-0
mysql-1
mysql-2
```

If `mysql-1` is deleted, Kubernetes creates `mysql-1` again.

### Simple StatefulSet

```yaml
apiVersion: apps/v1
kind: StatefulSet

metadata:
  name: mysql

spec:
  serviceName: mysql
  replicas: 2

  selector:
    matchLabels:
      app: mysql

  template:
    metadata:
      labels:
        app: mysql

    spec:
      containers:
      - name: mysql
        image: mysql:8
```

Create:

```bash
kubectl apply -f statefulset.yaml
```

Check:

```bash
kubectl get statefulset
kubectl get pods
```

---

# 2. Namespace

A **Namespace** is used to separate and organize resources in a Kubernetes cluster.

Example:

```text
Cluster
│
├── development
├── testing
└── production
```

### Create Namespace

```bash
kubectl create namespace development
```

Check:

```bash
kubectl get namespaces
```

### Create Pod in Namespace

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx
  namespace: development

spec:
  containers:
  - name: nginx
    image: nginx
```

Apply:

```bash
kubectl apply -f pod.yaml
```

Check:

```bash
kubectl get pods -n development
```

---

# 3. ConfigMap

A **ConfigMap** stores **non-sensitive configuration data**.

Examples:

```text
APP_NAME
APP_ENV
APP_PORT
DATABASE_HOST
```

Do not store passwords in ConfigMap. Use **Secret** for sensitive data.

### Create ConfigMap

```bash
kubectl create configmap app-config \
  --from-literal=APP_NAME=myapp \
  --from-literal=APP_ENV=dev
```

Check:

```bash
kubectl get configmap
```

### ConfigMap YAML

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: app-config

data:
  APP_NAME: myapp
  APP_ENV: dev
  APP_PORT: "8080"
```

Apply:

```bash
kubectl apply -f configmap.yaml
```

---

## Use ConfigMap in Pod

```yaml
envFrom:
- configMapRef:
    name: app-config
```

Example:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: config-pod

spec:
  containers:
  - name: nginx
    image: nginx
    envFrom:
    - configMapRef:
        name: app-config
```

Check environment variables:

```bash
kubectl exec -it config-pod -- env
```

---

# Quick Revision

| Resource    | Purpose                           |
| ----------- | --------------------------------- |
| StatefulSet | Stateful applications             |
| Namespace   | Organize and separate resources   |
| ConfigMap   | Store non-sensitive configuration |
| Secret      | Store sensitive information       |

```text
Deployment  → Stateless application
StatefulSet → Stateful application
Namespace   → Resource separation
ConfigMap   → Configuration
Secret      → Sensitive data
```
