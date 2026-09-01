# Class 41 – Kubernetes YAML & Manifest Files
class url: https://youtu.be/tfEdg4p0ZfM
## 1. What is YAML?

**YAML** stands for **YAML Ain't Markup Language**.

YAML is a human-readable format commonly used to define Kubernetes resources.

Kubernetes uses YAML files to describe the **desired state** of resources.

Example:

```yaml
name: frontend
replicas: 3
```

---

# 2. What is a Manifest File?

A **Kubernetes Manifest File** is a YAML file that defines a Kubernetes resource.

For example, we can create manifest files for:

* Pod
* Deployment
* Service
* ConfigMap
* Secret
* Namespace
* Ingress
* StatefulSet
* DaemonSet

Example:

```text
pod.yaml
deployment.yaml
service.yaml
```

We apply the manifest using:

```bash
kubectl apply -f pod.yaml
```

---

# 3. Basic Structure of a Kubernetes YAML File

Most Kubernetes YAML files contain these important sections:

```yaml
apiVersion:
kind:
metadata:
spec:
```

Example:

```yaml
apiVersion: v1

kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
    - name: nginx
      image: nginx
```

### Explanation

| Field        | Meaning                               |
| ------------ | ------------------------------------- |
| `apiVersion` | Kubernetes API version                |
| `kind`       | Type of Kubernetes resource           |
| `metadata`   | Name, labels and other information    |
| `spec`       | Desired configuration of the resource |

---

# 4. YAML Indentation

YAML uses **spaces for indentation**.

Indentation is very important.

Correct:

```yaml
metadata:
  name: nginx-pod
```

Incorrect:

```yaml
metadata:
name: nginx-pod
```

Recommended:

```text
Use 2 spaces for indentation.
Do not use TAB.
```

---

# 5. Key-Value Format

YAML mainly uses:

```text
key: value
```

Example:

```yaml
name: nginx
replicas: 3
image: nginx
```

---

# 6. Lists in YAML

A list is represented using `-`.

Example:

```yaml
containers:
  - name: nginx
    image: nginx
```

Multiple items:

```yaml
containers:
  - name: nginx
    image: nginx

  - name: sidecar
    image: busybox
```

---

# 7. How to Create a Pod Manifest

Let's create a simple Pod running Nginx.

Create a file:

```bash
vi pod.yaml
```

Add:

```yaml
apiVersion: v1

kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
```

Save the file.

---

# 8. Pod Manifest Explanation

### apiVersion

```yaml
apiVersion: v1
```

For a basic Pod, we use:

```text
v1
```

### kind

```yaml
kind: Pod
```

This tells Kubernetes that we want to create a **Pod**.

### metadata

```yaml
metadata:
  name: nginx-pod
```

This gives the Pod its name.

### spec

```yaml
spec:
```

`spec` contains the desired configuration of the Pod.

### containers

```yaml
containers:
```

Defines the containers that should run inside the Pod.

### Container name

```yaml
- name: nginx
```

Name of the container.

### Image

```yaml
image: nginx
```

Docker/container image used to create the container.

### Container Port

```yaml
ports:
  - containerPort: 80
```

Defines the port used by the container.

---

# 9. Create the Pod

Apply the manifest:

```bash
kubectl apply -f pod.yaml
```

Output:

```text
pod/nginx-pod created
```

Check the Pod:

```bash
kubectl get pods
```

Detailed information:

```bash
kubectl describe pod nginx-pod
```

---

# 10. Delete the Pod

```bash
kubectl delete -f pod.yaml
```

Or:

```bash
kubectl delete pod nginx-pod
```

---

# 11. Pod Manifest – Complete Example

```yaml
apiVersion: v1

kind: Pod

metadata:
  name: nginx-pod
  labels:
    app: nginx

spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
```

Here we also added a label:

```yaml
labels:
  app: nginx
```

Labels are used to identify and select Kubernetes resources.

---

# 12. What is a Deployment?

A **Deployment** manages Pods.

Instead of creating individual Pods manually, we normally use a Deployment for applications.

Deployment provides:

* Multiple replicas
* Scaling
* Rolling updates
* Rollbacks
* Self-healing through ReplicaSets

Architecture:

```text
Deployment
     |
     ↓
ReplicaSet
     |
     +---- Pod
     +---- Pod
     +---- Pod
```

---

# 13. Deployment Manifest

Create a file:

```bash
vi deployment.yaml
```

Add:

```yaml
apiVersion: apps/v1

kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```

---

# 14. Deployment Manifest Explanation

## apiVersion

```yaml
apiVersion: apps/v1
```

Deployments use:

```text
apps/v1
```

---

## kind

```yaml
kind: Deployment
```

This tells Kubernetes that we want to create a Deployment.

---

## metadata

```yaml
metadata:
  name: nginx-deployment
```

Name of the Deployment.

---

## replicas

```yaml
replicas: 3
```

We want Kubernetes to maintain **3 Pods**.

```text
Deployment
     |
 ReplicaSet
     |
 +---+---+---+
 |   |   |   |
Pod Pod Pod
```

---

# 15. Selector

```yaml
selector:
  matchLabels:
    app: nginx
```

The selector tells the Deployment which Pods it manages.

The selector must match the labels in the Pod template.

```yaml
selector:
  matchLabels:
    app: nginx

template:
  metadata:
    labels:
      app: nginx
```

Here:

```text
selector label = app: nginx
template label = app: nginx
```

They match.

---

# 16. Template

The `template` defines the Pod that the Deployment will create.

```yaml
template:
  metadata:
    labels:
      app: nginx

  spec:
    containers:
      - name: nginx
        image: nginx
```

Think of it as:

```text
Deployment
     |
     ↓
Pod Template
     |
     ↓
Creates Pods
```

---

# 17. Create Deployment

Apply the manifest:

```bash
kubectl apply -f deployment.yaml
```

Check Deployment:

```bash
kubectl get deployments
```

Check ReplicaSet:

```bash
kubectl get replicasets
```

Check Pods:

```bash
kubectl get pods
```

---

# 18. Expected Output

```text
NAME               READY   UP-TO-DATE   AVAILABLE
nginx-deployment   3/3     3            3
```

Pods:

```text
NAME                                READY   STATUS
nginx-deployment-xxxxx-xxxxx        1/1     Running
nginx-deployment-xxxxx-yyyyy        1/1     Running
nginx-deployment-xxxxx-zzzzz        1/1     Running
```

The Pod names are automatically generated by Kubernetes.

---

# 19. Deployment Architecture

```text
              Deployment
                   |
                   ↓
              ReplicaSet
                   |
        +----------+----------+
        |          |          |
        ↓          ↓          ↓
      Pod 1      Pod 2      Pod 3
        |          |          |
      nginx      nginx      nginx
```

---

# 20. Scale Deployment

We initially created:

```yaml
replicas: 3
```

We can change it to:

```yaml
replicas: 5
```

Then apply:

```bash
kubectl apply -f deployment.yaml
```

Now Kubernetes creates 5 Pods.

Or use the command:

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

Check:

```bash
kubectl get pods
```

---

# 21. Update Image

Change:

```yaml
image: nginx:latest
```

For example:

```yaml
image: nginx:1.27
```

Then:

```bash
kubectl apply -f deployment.yaml
```

Kubernetes performs a rolling update.

---

# 22. Validate YAML Before Applying

We can check the manifest using:

```bash
kubectl apply --dry-run=client -f pod.yaml
```

For Deployment:

```bash
kubectl apply --dry-run=client -f deployment.yaml
```

This helps identify configuration problems before creating the resource.

---

# 23. Useful Commands

### Apply YAML

```bash
kubectl apply -f pod.yaml
```

### Apply Deployment

```bash
kubectl apply -f deployment.yaml
```

### Create resource from YAML

```bash
kubectl create -f pod.yaml
```

### Check Pods

```bash
kubectl get pods
```

### Check Deployment

```bash
kubectl get deployment
```

### Check ReplicaSets

```bash
kubectl get rs
```

### Detailed Pod information

```bash
kubectl describe pod nginx-pod
```

### Delete resource using YAML

```bash
kubectl delete -f pod.yaml
```

### Check YAML syntax/configuration

```bash
kubectl apply --dry-run=client -f pod.yaml
```

---

# 24. Pod vs Deployment

| Pod                                             | Deployment                     |
| ----------------------------------------------- | ------------------------------ |
| Runs containers                                 | Manages Pods                   |
| Smallest deployable unit                        | Higher-level resource          |
| Usually one application Pod                     | Can manage multiple Pods       |
| No automatic replica management                 | Maintains desired replicas     |
| Not ideal for production application management | Commonly used for applications |

---

# 25. Important YAML Rules

### Rule 1 – Use spaces

```yaml
metadata:
  name: nginx
```

Do not use TAB.

### Rule 2 – Indentation matters

```yaml
spec:
  containers:
    - name: nginx
```

### Rule 3 – Use correct apiVersion

Pod:

```yaml
apiVersion: v1
```

Deployment:

```yaml
apiVersion: apps/v1
```

### Rule 4 – Use correct kind

```yaml
kind: Pod
```

```yaml
kind: Deployment
```

### Rule 5 – Deployment selector and labels must match

```yaml
selector:
  matchLabels:
    app: nginx

template:
  metadata:
    labels:
      app: nginx
```

### Rule 6 – Avoid duplicate YAML keys

Incorrect:

```yaml
ports:
  - containerPort: 3000
    containerPort: 80
```

Correct:

```yaml
ports:
  - containerPort: 80
```

---

# 26. Simple YAML Structure to Remember

For a Pod:

```text
apiVersion
    ↓
kind
    ↓
metadata
    ↓
spec
    ↓
containers
    ↓
name + image
```

Example:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
    - name: nginx
      image: nginx
```

For a Deployment:

```text
apiVersion
    ↓
kind
    ↓
metadata
    ↓
spec
    ↓
replicas
    ↓
selector
    ↓
template
    ↓
Pod specification
```

---

# 27. Final Revision

### Pod

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
    - name: nginx
      image: nginx
```

### Deployment

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx
          image: nginx
```

### Basic Workflow

```text
Write YAML
    ↓
Save manifest file
    ↓
Validate YAML
    ↓
kubectl apply -f file.yaml
    ↓
Kubernetes creates resource
    ↓
kubectl get pods
    ↓
Check application
```

### Important Commands

```bash
kubectl apply -f pod.yaml
kubectl apply -f deployment.yaml

kubectl get pods
kubectl get deployment
kubectl get rs

kubectl describe pod <pod-name>
kubectl describe deployment <deployment-name>

kubectl delete -f pod.yaml
kubectl delete -f deployment.yaml
```
