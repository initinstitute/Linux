# Class 43 – Kubernetes Deployment, DaemonSet, StatefulSet & Services
class url : https://youtu.be/J1l62Q8mP5w
## 1. Kubernetes Workloads

Kubernetes provides different workload resources to manage applications.

Main workload resources:

* Deployment
* DaemonSet
* StatefulSet
* Job
* CronJob

In this class:

* Deployment
* DaemonSet
* StatefulSet

---

# 2. Deployment

A **Deployment** is used to deploy and manage **stateless applications** in Kubernetes.

Examples:

* Nginx
* React frontend
* Django backend
* Java application

A Deployment manages ReplicaSets, and ReplicaSets manage Pods.

### Architecture

```text
Deployment
    |
    v
ReplicaSet
    |
    +---- Pod
    +---- Pod
    +---- Pod
```

### Example Deployment

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

### Create Deployment

```bash
kubectl apply -f deployment.yaml
```

### Check Deployment

```bash
kubectl get deployments
```

```bash
kubectl get pods
```

```bash
kubectl get rs
```

### Scaling Deployment

Increase replicas:

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

Decrease replicas:

```bash
kubectl scale deployment nginx-deployment --replicas=2
```

### Update Image

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.29
```

### Rollout Status

```bash
kubectl rollout status deployment/nginx-deployment
```

### Rollout History

```bash
kubectl rollout history deployment/nginx-deployment
```

### Rollback

```bash
kubectl rollout undo deployment/nginx-deployment
```

---

# 3. DaemonSet

A **DaemonSet** ensures that a copy of a Pod runs on **each node** that matches its scheduling rules.

Example:

```text
Node 1 → Pod
Node 2 → Pod
Node 3 → Pod
```

If a new node is added:

```text
Node 1 → Pod
Node 2 → Pod
Node 3 → Pod
Node 4 → Pod  ← Automatically created
```

### Common Uses

DaemonSets are commonly used for:

* Log collection
* Monitoring agents
* Node monitoring
* Networking agents
* Security agents

Examples:

* Fluent Bit
* Prometheus Node Exporter
* CNI networking components

### DaemonSet Example

```yaml
apiVersion: apps/v1
kind: DaemonSet

metadata:
  name: nginx-daemonset

spec:
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

### Create DaemonSet

```bash
kubectl apply -f daemonset.yaml
```

### Check DaemonSet

```bash
kubectl get daemonsets
```

```bash
kubectl get pods -o wide
```

The Pods should normally appear across the eligible nodes.

### Important Point

With a Deployment:

```text
replicas: 3
```

means **3 Pods total**.

With a DaemonSet:

```text
1 Pod per eligible node
```

---

# 4. StatefulSet

A **StatefulSet** is used for applications that require **stable identity and persistent storage**.

It is mainly used for **stateful applications**.

Examples:

* MySQL
* PostgreSQL
* MongoDB
* Redis
* Kafka

### Deployment vs StatefulSet

Deployment Pods generally have replaceable identities:

```text
nginx-7d8f9c6b5-x1abc
nginx-7d8f9c6b5-x2def
```

StatefulSet Pods have stable names:

```text
mysql-0
mysql-1
mysql-2
```

If `mysql-1` is deleted, Kubernetes recreates:

```text
mysql-1
```

rather than creating a completely new identity.

### StatefulSet Features

* Stable Pod names
* Stable network identity
* Ordered Pod creation
* Ordered Pod termination
* Persistent storage
* Suitable for databases and clustered applications

### Example StatefulSet

```yaml
apiVersion: apps/v1
kind: StatefulSet

metadata:
  name: nginx-statefulset

spec:
  serviceName: nginx-headless
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

### Create StatefulSet

```bash
kubectl apply -f statefulset.yaml
```

### Check StatefulSet

```bash
kubectl get statefulsets
```

```bash
kubectl get pods
```

Pods will have names similar to:

```text
nginx-statefulset-0
nginx-statefulset-1
nginx-statefulset-2
```

---

# 5. Deployment vs DaemonSet vs StatefulSet

| Feature            | Deployment        | DaemonSet             | StatefulSet       |
| ------------------ | ----------------- | --------------------- | ----------------- |
| Application type   | Stateless         | Node-level service    | Stateful          |
| Pod count          | Based on replicas | One per eligible node | Based on replicas |
| Stable identity    | No                | No                    | Yes               |
| Stable hostname    | No                | No                    | Yes               |
| Persistent storage | Optional          | Optional              | Common            |
| Pod ordering       | No                | No                    | Yes               |
| Common use         | Web applications  | Monitoring/logging    | Databases         |
| Example            | Nginx             | Fluent Bit            | PostgreSQL        |

---

# 6. Kubernetes Services

A **Service** provides a stable network endpoint to access Pods.

Pods are temporary and their IP addresses can change.

Instead of accessing Pods directly, we normally access them through a Service.

```text
Client
   |
   v
Service
   |
   +---- Pod
   +---- Pod
   +---- Pod
```

A Service uses **labels/selectors** to identify the Pods.

Example:

```yaml
selector:
  app: nginx
```

The Service sends traffic to Pods having:

```yaml
labels:
  app: nginx
```

---

# 7. Types of Kubernetes Services

Important Service types:

1. ClusterIP
2. NodePort
3. LoadBalancer
4. Headless Service
5. ExternalName

---

# 8. ClusterIP

**ClusterIP** is the default Service type.

It provides access to the application **inside the Kubernetes cluster**.

```text
Pod A
  |
  v
ClusterIP Service
  |
  +---- Pod
  +---- Pod
```

It is normally not directly accessible from the internet.

### ClusterIP Example

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-service

spec:
  type: ClusterIP

  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
```

### Create Service

```bash
kubectl apply -f service.yaml
```

### Check Service

```bash
kubectl get services
```

or:

```bash
kubectl get svc
```

Example:

```text
NAME            TYPE        CLUSTER-IP      PORT(S)
nginx-service   ClusterIP   10.96.100.10    80/TCP
```

### Use Case

ClusterIP is commonly used for:

```text
Frontend → Backend
Backend  → Database
```

Example:

```text
frontend
   |
   v
backend-clusterip
   |
   v
backend Pods
```

---

# 9. NodePort

A **NodePort** exposes a Service through a port on each Kubernetes node.

```text
Client
   |
   v
Node IP : NodePort
   |
   v
Service
   |
   v
Pods
```

NodePort normally uses a port from:

```text
30000 - 32767
```

### NodePort Example

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-nodeport

spec:
  type: NodePort

  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080
```

Access:

```text
http://NODE-IP:30080
```

For example:

```text
http://192.168.1.10:30080
```

### Check

```bash
kubectl get svc
```

Example:

```text
NAME             TYPE       CLUSTER-IP      PORT(S)
nginx-nodeport   NodePort   10.96.100.20    80:30080/TCP
```

Meaning:

```text
Service Port = 80
NodePort     = 30080
```

---

# 10. LoadBalancer

A **LoadBalancer** Service exposes an application using an external load balancer provided by the cloud/platform.

Common in:

* AWS
* Azure
* Google Cloud

Architecture:

```text
Internet
    |
    v
Cloud Load Balancer
    |
    v
Kubernetes Service
    |
    +---- Pod
    +---- Pod
    +---- Pod
```

### Example

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-loadbalancer

spec:
  type: LoadBalancer

  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
```

Create:

```bash
kubectl apply -f loadbalancer.yaml
```

Check:

```bash
kubectl get svc
```

In a cloud environment, Kubernetes may provision a cloud load balancer and show an external address.

---

# 11. Headless Service

A **Headless Service** does not provide a normal ClusterIP.

It is created using:

```yaml
clusterIP: None
```

### Example

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-headless

spec:
  clusterIP: None

  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
```

Check:

```bash
kubectl get svc
```

Example:

```text
NAME            TYPE        CLUSTER-IP
nginx-headless  ClusterIP   None
```

### Why use Headless Service?

Headless Services are commonly used with **StatefulSets**.

They allow clients to discover individual Pods.

Example:

```text
mysql-0
mysql-1
mysql-2
```

DNS names can be created for individual StatefulSet Pods.

Conceptually:

```text
mysql-0.mysql-headless
mysql-1.mysql-headless
mysql-2.mysql-headless
```

This is useful for databases and distributed applications where individual Pod identity matters.

---

# 12. ExternalName

**ExternalName** is used to create a Kubernetes Service that points to an **external DNS name**.

It does not select Kubernetes Pods.

### Example

```yaml
apiVersion: v1
kind: Service

metadata:
  name: external-db

spec:
  type: ExternalName
  externalName: database.example.com
```

Now applications inside Kubernetes can use:

```text
external-db
```

instead of directly using:

```text
database.example.com
```

### Use Case

For example, an application is running in Kubernetes but the database is outside the cluster:

```text
Kubernetes Application
        |
        v
external-db Service
        |
        v
database.example.com
        |
        v
External Database
```

---

# 13. Service Types Comparison

| Service Type | Cluster Access | External Access             | ClusterIP | Main Use                    |
| ------------ | -------------- | --------------------------- | --------- | --------------------------- |
| ClusterIP    | Yes            | No                          | Yes       | Internal applications       |
| NodePort     | Yes            | Yes, through Node IP + port | Yes       | Simple external access      |
| LoadBalancer | Yes            | Yes                         | Yes       | Cloud load balancer         |
| Headless     | Yes            | No                          | None      | Pod discovery / StatefulSet |
| ExternalName | Yes            | Indirectly                  | DNS-based | External services           |

---

# 14. Complete Example

## Deployment

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx

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

## ClusterIP Service

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-service

spec:
  type: ClusterIP

  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
```

Apply:

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

Check:

```bash
kubectl get deployment
kubectl get pods
kubectl get svc
```

---

# 15. Useful kubectl Commands

### Deployment

```bash
kubectl get deployment
kubectl describe deployment <deployment-name>
kubectl scale deployment <deployment-name> --replicas=3
kubectl rollout status deployment <deployment-name>
kubectl rollout history deployment <deployment-name>
kubectl rollout undo deployment <deployment-name>
```

### DaemonSet

```bash
kubectl get daemonset
kubectl describe daemonset <daemonset-name>
```

### StatefulSet

```bash
kubectl get statefulset
kubectl describe statefulset <statefulset-name>
```

### Services

```bash
kubectl get svc
kubectl describe svc <service-name>
```

### Get Pods with Labels

```bash
kubectl get pods --show-labels
```

### Get Endpoints

```bash
kubectl get endpoints
```

For newer Kubernetes versions, EndpointSlices can also be checked:

```bash
kubectl get endpointslices
```

---

# 16. Quick Revision

### Deployment

```text
Used for stateless applications
        |
        v
ReplicaSet
        |
        v
Pods
```

### DaemonSet

```text
One Pod on each eligible Node
```

### StatefulSet

```text
Stable Pod identity
Stable network identity
Persistent storage
```

### ClusterIP

```text
Internal cluster access
```

### NodePort

```text
Node IP + Port
```

### LoadBalancer

```text
Cloud Load Balancer
```

### Headless Service

```text
clusterIP: None
Pod discovery
Commonly used with StatefulSet
```

### ExternalName

```text
Kubernetes Service
       |
       v
External DNS name
```

# Important Difference

```text
Deployment
    → Stateless applications

DaemonSet
    → One Pod per eligible Node

StatefulSet
    → Stateful applications with stable identity

ClusterIP
    → Internal access

NodePort
    → Node IP + Port

LoadBalancer
    → External Cloud Load Balancer

Headless
    → Direct Pod discovery / StatefulSet

ExternalName
    → External DNS service
```
