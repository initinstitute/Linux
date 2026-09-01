# Class 40 – Kubernetes Architecture 
class url : https://youtu.be/MWEz0_5dY_U
## 1. Introduction to Kubernetes

**Kubernetes (K8s)** is an open-source container orchestration platform used to:

* Deploy containerized applications
* Manage containers
* Scale applications
* Provide high availability
* Perform rolling updates and rollbacks
* Manage networking
* Manage configuration and secrets
* Automatically restart failed containers

### Why Kubernetes?

Docker can run containers, but managing many containers across multiple servers manually becomes difficult.

```text
Application
    ↓
Docker Containers
    ↓
Multiple Servers
    ↓
Kubernetes
```

Kubernetes automates the management of these containers.

---

# 2. Kubernetes Cluster

A **Kubernetes Cluster** is a group of machines that work together to run containerized applications.

A Kubernetes cluster mainly contains:

1. **Control Plane**
2. **Worker Nodes**

```text
                  Kubernetes Cluster
                         |
          +--------------+--------------+
          |                             |
     Control Plane                 Worker Nodes
          |                             |
    Manage Cluster              Run Applications
```

---

# 3. Kubernetes Architecture

```text
                         Kubernetes Cluster
                                |
              +-----------------+-----------------+
              |                                   |
        Control Plane                         Worker Nodes
              |                              +-------------+
    +---------+---------+                    |             |
    |         |         |                  Node 1        Node 2
    |         |         |                    |             |
 API Server  Scheduler  Controller          Pods          Pods
    |                   Manager
    |
    +------ etcd
```

---

# 4. Control Plane

The **Control Plane** is responsible for managing the Kubernetes cluster.

It makes decisions such as:

* Where should a Pod run?
* How many replicas should run?
* Is the desired state matching the actual state?
* Should a failed Pod be recreated?

### Main Control Plane Components

```text
Control Plane
│
├── kube-apiserver
├── etcd
├── kube-scheduler
├── kube-controller-manager
└── cloud-controller-manager (optional)
```

---

# 5. kube-apiserver

The **kube-apiserver** is the main entry point to the Kubernetes cluster.

When we execute:

```bash
kubectl get pods
```

The request goes to:

```text
kubectl
   ↓
kube-apiserver
   ↓
Kubernetes Cluster
```

### Responsibilities

* Accept API requests
* Authenticate users
* Authorize requests
* Validate Kubernetes objects
* Communicate with other Kubernetes components

Example:

```bash
kubectl create deployment nginx --image=nginx
```

The request is sent to the **API Server**.

---

# 6. etcd

**etcd** is a distributed key-value database used by Kubernetes.

It stores the Kubernetes cluster state.

For example:

* Nodes
* Pods
* Deployments
* Services
* ConfigMaps
* Secrets
* Cluster configuration

```text
Kubernetes
     |
 API Server
     |
   etcd
     |
Cluster State
```

### Important

> etcd stores the state and configuration of the Kubernetes cluster.

Regular backups of etcd are important for cluster recovery.

---

# 7. kube-scheduler

The **kube-scheduler** decides **which worker node should run a newly created Pod**.

```text
New Pod
   |
   ↓
Scheduler
   |
   +---- Worker Node 1
   |
   +---- Worker Node 2
   |
   +---- Worker Node 3
```

The scheduler considers factors such as:

* Available CPU
* Available memory
* Node requirements
* Taints and tolerations
* Affinity/anti-affinity
* Other scheduling rules

Example:

```text
Pod
 ↓
Scheduler
 ↓
Node 2
```

---

# 8. kube-controller-manager

The **kube-controller-manager** runs various Kubernetes controllers.

Controllers continuously compare:

```text
Desired State
      vs
Actual State
```

If there is a difference, controllers try to correct it.

### Example

Suppose we create:

```yaml
replicas: 3
```

Kubernetes should maintain:

```text
Desired = 3 Pods
```

If one Pod fails:

```text
Before:

Pod 1
Pod 2
Pod 3

Pod 2 fails

After:

Pod 1
Pod 3
Pod 4 ← Created automatically
```

The controller maintains the desired number of Pods.

---

# 9. Cloud Controller Manager

The **cloud-controller-manager** is used when Kubernetes integrates with a cloud provider such as AWS.

It can manage cloud-specific resources such as:

* Load Balancers
* Cloud nodes
* Cloud networking
* Cloud storage

```text
Kubernetes
     |
Cloud Controller Manager
     |
     ↓
Cloud Provider
     |
Load Balancer / Cloud Resources
```

It is generally used in cloud-based Kubernetes environments.

---

# 10. Worker Node

A **Worker Node** is the machine where application workloads actually run.

A worker node contains:

```text
Worker Node
│
├── kubelet
├── kube-proxy
├── Container Runtime
└── Pods
```

Examples:

```text
EC2 Instance 1
EC2 Instance 2
EC2 Instance 3
```

---

# 11. kubelet

**kubelet** is an agent that runs on every worker node.

Its main responsibility is to make sure the Pods assigned to the node are running correctly.

```text
Control Plane
      |
 API Server
      |
    kubelet
      |
     Pod
```

### kubelet Responsibilities

* Communicates with the API Server
* Creates Pods
* Starts containers
* Monitors containers
* Reports Pod and node status
* Restarts containers when required

---

# 12. Container Runtime

The **Container Runtime** is responsible for actually running containers.

Examples:

* containerd
* CRI-O

Kubernetes uses the **CRI (Container Runtime Interface)** to communicate with container runtimes.

```text
kubelet
   ↓
CRI
   ↓
containerd / CRI-O
   ↓
Container
```

---

# 13. kube-proxy

**kube-proxy** runs on worker nodes and helps implement Kubernetes Service networking.

It helps route network traffic to the appropriate Pods.

```text
Client
  |
  ↓
Service
  |
  ↓
kube-proxy
  |
  +---- Pod 1
  +---- Pod 2
  +---- Pod 3
```

It helps provide networking functionality for Kubernetes Services.

---

# 14. Pod

A **Pod** is the smallest deployable unit in Kubernetes.

A Pod contains one or more containers.

Most commonly:

```text
Pod
 |
 └── Container
```

Example:

```text
Worker Node
    |
    +── Pod
         |
         └── nginx container
```

Multiple containers can also exist in the same Pod:

```text
Pod
 |
 +── Application Container
 |
 └── Sidecar Container
```

Containers inside the same Pod share networking and can share storage volumes.

---

# 15. Kubernetes Objects

Kubernetes uses objects to describe the desired state of applications.

Common Kubernetes objects:

```text
Pod
Deployment
ReplicaSet
Service
ConfigMap
Secret
Namespace
DaemonSet
StatefulSet
Job
CronJob
Ingress
```

Example:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: frontend

spec:
  replicas: 3
```

This tells Kubernetes that we want **3 replicas** of the application.

---

# 16. Deployment

A **Deployment** manages application Pods.

```text
Deployment
     |
 ReplicaSet
     |
 +---+---+
 |   |   |
Pod Pod Pod
```

Deployment provides:

* Replica management
* Rolling updates
* Rollbacks
* Scaling

Example:

```bash
kubectl scale deployment frontend --replicas=5
```

---

# 17. ReplicaSet

A **ReplicaSet** ensures that the required number of Pod replicas are running.

```text
ReplicaSet
     |
     +── Pod 1
     +── Pod 2
     +── Pod 3
```

If one Pod is deleted:

```text
Pod 1
Pod 2
Pod 3

Pod 2 deleted

ReplicaSet
     |
     +── Pod 1
     +── Pod 3
     +── New Pod
```

---

# 18. Kubernetes Service

Pods are temporary and their IP addresses can change.

A **Service** provides a stable way to access Pods.

```text
Client
  |
  ↓
Service
  |
  +---- Pod 1
  +---- Pod 2
  +---- Pod 3
```

### Common Service Types

```text
ClusterIP
NodePort
LoadBalancer
ExternalName
```

---

# 19. Namespace

A **Namespace** provides logical separation inside a Kubernetes cluster.

```text
Kubernetes Cluster
│
├── default
├── development
├── testing
├── production
└── monitoring
```

Create a namespace:

```bash
kubectl create namespace development
```

Get Pods from a namespace:

```bash
kubectl get pods -n development
```

---

# 20. Kubernetes API Communication

Most Kubernetes components communicate through the **API Server**.

```text
                    kube-apiserver
                   /      |       \
                  /       |        \
               etcd   Scheduler   Controller
                  |
              kubelet
                  |
               Worker
                  |
                 Pod
```

For example:

```bash
kubectl get pods
```

The flow is approximately:

```text
User
 ↓
kubectl
 ↓
API Server
 ↓
Kubernetes Resources
 ↓
Response
 ↓
kubectl
 ↓
User
```

---

# 21. Complete Kubernetes Architecture

```text
                         KUBERNETES CLUSTER
                                |
             +------------------+------------------+
             |                                     |
        CONTROL PLANE                         WORKER NODE
             |                                     |
     +-------+--------+                    +-------+-------+
     |       |        |                    |       |       |
 API Server etcd  Scheduler          kubelet  kube-proxy
     |       |        |                    |
     |       |        |              Container Runtime
     |       |        |                    |
     |       |        |                  Pods
     |       |        |               +----+----+
     |       |        |               |         |
     |       |        |            Container  Container
     |       |        |
     |  Controller Manager
     |
     +---- Cloud Controller Manager
```

---

# 22. Request Flow Example

Suppose we create a Deployment:

```bash
kubectl create deployment nginx --image=nginx
```

The flow is:

```text
User
  |
  ↓
kubectl
  |
  ↓
kube-apiserver
  |
  +------> etcd
  |
  ↓
Controller Manager
  |
  ↓
ReplicaSet
  |
  ↓
Pod
  |
  ↓
Scheduler
  |
  ↓
Worker Node
  |
  ↓
kubelet
  |
  ↓
Container Runtime
  |
  ↓
nginx Container
```

---

# 23. Important Kubernetes Commands

### Check cluster information

```bash
kubectl cluster-info
```

### List nodes

```bash
kubectl get nodes
```

### List Pods

```bash
kubectl get pods
```

### List Pods in all namespaces

```bash
kubectl get pods -A
```

### List deployments

```bash
kubectl get deployments
```

### List services

```bash
kubectl get services
```

### List Kubernetes resource types

```bash
kubectl api-resources
```

### List common resources

```bash
kubectl get all
```

---

# 24. Control Plane vs Worker Node

| Control Plane              | Worker Node       |
| -------------------------- | ----------------- |
| Manages cluster            | Runs applications |
| kube-apiserver             | kubelet           |
| etcd                       | kube-proxy        |
| kube-scheduler             | Container Runtime |
| kube-controller-manager    | Pods              |
| Makes scheduling decisions | Runs containers   |

---

# 25. Quick Revision

```text
Kubernetes Cluster
       |
       +---- Control Plane
       |       |
       |       +---- API Server
       |       +---- etcd
       |       +---- Scheduler
       |       +---- Controller Manager
       |
       +---- Worker Nodes
               |
               +---- kubelet
               +---- kube-proxy
               +---- Container Runtime
               +---- Pods
```

### Easy Memory Trick

**Control Plane = Manage**

**Worker Node = Run**

```text
Control Plane
     ↓
Controls and manages the cluster

Worker Node
     ↓
Runs application containers
```

### Key Components

| Component          | Main Responsibility                    |
| ------------------ | -------------------------------------- |
| API Server         | Entry point to Kubernetes              |
| etcd               | Stores cluster state                   |
| Scheduler          | Selects worker node for Pods           |
| Controller Manager | Maintains desired state                |
| kubelet            | Manages Pods on worker nodes           |
| kube-proxy         | Service networking                     |
| Container Runtime  | Runs containers                        |
| Pod                | Smallest deployable unit               |
| Deployment         | Manages application Pods               |
| Service            | Provides stable network access to Pods |
