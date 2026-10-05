# Class 54 – Pod Eviction
class url: https://youtu.be/KPiUMYFcsUI
## Pod Eviction

**Eviction** is the process of gracefully removing a Pod from a node.

The Pod may then be recreated or scheduled on another node, depending on the workload controller.

### Types of Eviction

1. **Voluntary Eviction**

   * Intentionally initiated by an administrator or Kubernetes policy.
   * Example:

     * `kubectl drain`
     * PodDisruptionBudget (PDB)

2. **Involuntary Eviction**

   * Automatically caused by Kubernetes because of node or resource problems.
   * Example:

     * Memory pressure
     * Disk pressure
     * Node problems
     * Pod preemption

---

# Voluntary Eviction

Voluntary eviction is an intentional disruption where Kubernetes tries to terminate Pods gracefully.

### How It Works

1. Eviction is requested.
2. Kubernetes checks the **PodDisruptionBudget (PDB)**.
3. If the eviction does not violate the PDB, Kubernetes proceeds.
4. The Pod is marked for termination.
5. Kubelet sends **SIGTERM** to the containers.
6. The Pod waits for `terminationGracePeriodSeconds`.
7. Default grace period is **30 seconds**.
8. If the container does not terminate, it is forcefully killed using **SIGKILL**.
9. The Pod is removed.
10. If the Pod is managed by a Deployment, StatefulSet, etc., a replacement Pod can be created and scheduled on another node.

---

# Node Drain

`kubectl drain` is commonly used before performing maintenance on a node.

It:

* Marks the node as **unschedulable**.
* Evicts eligible Pods from the node.
* Allows Pods to be recreated on other nodes.

### STEP 1: Cordon the Node

The node is marked as unschedulable.

```bash
kubectl cordon <node-name>
```

After cordoning:

* New Pods will not normally be scheduled on the node.
* Existing Pods continue running.

`kubectl drain` performs this cordon operation automatically.

---

### STEP 2: Evict Pods

`kubectl drain` attempts to evict Pods that can be safely removed.

Example:

```bash
kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
```

The Kubernetes **Eviction API** can be represented as:

```yaml
apiVersion: policy/v1
kind: Eviction
metadata:
  name: <pod-name>
  namespace: <namespace>
deleteOptions:
  gracePeriodSeconds: <value>
```

The eviction process allows Kubernetes to respect PodDisruptionBudgets.

---

### STEP 3: Pod Termination

After eviction is allowed:

1. Pod is marked for termination.
2. Kubelet sends `SIGTERM` to the containers.
3. Kubernetes waits for `terminationGracePeriodSeconds`.
4. Default is **30 seconds**.
5. If the container does not terminate, it is forcefully killed using `SIGKILL`.
6. The Pod is removed.
7. The workload controller may create a replacement Pod on another node.

---

# PodDisruptionBudget (PDB)

A **PodDisruptionBudget** controls how many Pods can be voluntarily disrupted at the same time.

Example:

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: my-app
```

### Meaning

```yaml
minAvailable: 2
```

means at least **2 matching Pods should remain available** during voluntary disruptions.

For example:

```text
Before drain:

Pod-1   Running
Pod-2   Running
Pod-3   Running

minAvailable: 2
```

Kubernetes can evict one Pod:

```text
Pod-1   Running
Pod-2   Running
Pod-3   Terminating
```

But it will not normally evict another matching Pod if doing so would violate the PDB.

---

# Eviction Failures

## 1. PodDisruptionBudget

If evicting a Pod would violate the PDB, the eviction can be blocked.

```text
PDB:
minAvailable: 2

Available Pods:
2

Evicting 1 Pod
     ↓
Available Pods would become 1
     ↓
PDB would be violated
     ↓
Eviction is blocked
```

---

## 2. emptyDir Volumes

Pods using `emptyDir` contain temporary data stored on the node.

During `kubectl drain`, Kubernetes requires confirmation before deleting this data.

Use:

```bash
kubectl drain <node-name> --delete-emptydir-data
```

**Important:** `emptyDir` does not protect the Pod from eviction. Its data is deleted when the Pod is removed.

---

## 3. DaemonSet Pods

DaemonSet Pods are normally not evicted by `kubectl drain`.

Use:

```bash
kubectl drain <node-name> --ignore-daemonsets
```

This tells `kubectl drain` to ignore DaemonSet-managed Pods.

---

## 4. Static Pods

Static Pods are managed directly by the **kubelet**, not by the Kubernetes API server scheduler.

Examples:

```text
kube-apiserver
etcd
kube-controller-manager
kube-scheduler
```

Static Pods are therefore not handled like normal workload Pods during a node drain.

---

# Complete Node Drain Command

A commonly used command is:

```bash
kubectl drain <node-name> \
  --ignore-daemonsets \
  --delete-emptydir-data
```

This:

1. Cordons the node.
2. Evicts eligible Pods.
3. Ignores DaemonSet Pods.
4. Allows deletion of `emptyDir` data.

---

# Involuntary Eviction

Involuntary evictions happen automatically because of resource pressure or other node conditions.

Common causes include:

### 1. Memory Pressure

If the node runs out of available memory, kubelet can evict Pods.

```text
Node Memory Pressure
        ↓
Kubelet detects pressure
        ↓
Pods are selected for eviction
        ↓
Pod is terminated
```

### 2. Disk Pressure

If the node has insufficient disk space or inodes, kubelet can evict Pods.

```text
Disk Pressure
     ↓
Kubelet detects pressure
     ↓
Pods are evicted
```

### 3. Pod Preemption

If a high-priority Pod cannot be scheduled because resources are unavailable, Kubernetes may evict lower-priority Pods to make room.

---

# How Kubernetes Selects Pods for Eviction

During **node-pressure eviction**, Kubernetes considers several factors.

## 1. Resource Usage vs Requests

Kubernetes considers whether Pods are using more resources than they requested.

## 2. Pod Priority

Lower-priority Pods are generally preferred for eviction over higher-priority Pods.

## 3. QoS Class

Pod QoS classes are:

```text
BestEffort
    ↓
Burstable
    ↓
Guaranteed
```

In general, **BestEffort Pods are more vulnerable to eviction than Guaranteed Pods**.

### QoS Classes

| QoS Class  | General Eviction Risk |
| ---------- | --------------------- |
| BestEffort | Highest               |
| Burstable  | Medium                |
| Guaranteed | Lowest                |

**Note:** The actual eviction ranking also considers resource usage relative to requests and Pod priority; QoS alone does not determine the final eviction order.

---

# Pod Priority

Pod Priority determines the importance of a Pod.

A Pod with higher priority is generally protected over a lower-priority Pod when resources are scarce.

Example:

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 100000
globalDefault: false
description: "High priority Pods"
```

Use it in a Pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  priorityClassName: high-priority
  containers:
    - name: nginx
      image: nginx
```

### Priority Example

```text
High Priority Pod
       ↓
Protected more

Low Priority Pod
       ↓
Evicted first when preemption is required
```

---

# Check Node Pressure

To check node conditions:

```bash
kubectl describe node <node-name>
```

Look for:

```text
Conditions:
  MemoryPressure
  DiskPressure
  PIDPressure
  Ready
```

You can also use:

```bash
kubectl describe node <node-name> | grep -i pressure
```

Example:

```text
MemoryPressure   False
DiskPressure     True
PIDPressure      False
```

This indicates that the node currently has **disk pressure**.

---

# Voluntary vs Involuntary Eviction

| Voluntary Eviction                | Involuntary Eviction                       |
| --------------------------------- | ------------------------------------------ |
| Intentional                       | Automatic                                  |
| Admin/Kubernetes policy initiated | Usually caused by node/resource conditions |
| `kubectl drain`                   | Memory pressure                            |
| PDB is respected                  | Disk pressure                              |
| Used for maintenance              | Node problems                              |
| Graceful termination              | Kubelet may force eviction                 |

---

# Important Commands

### Cordon a node

```bash
kubectl cordon <node-name>
```

### Drain a node

```bash
kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
```

### Uncordon a node

After maintenance:

```bash
kubectl uncordon <node-name>
```

### Check node status

```bash
kubectl get nodes
```

### Check node pressure

```bash
kubectl describe node <node-name> | grep -i pressure
```

### Check Pod status

```bash
kubectl get pods -o wide
```

---

# Summary

```text
Pod Eviction
     |
     +----------------------+
     |                      |
Voluntary              Involuntary
     |                      |
kubectl drain          Resource Pressure
PDB                    Node Problems
     |                 Preemption
     |
Graceful Termination
     |
SIGTERM
     |
terminationGracePeriodSeconds
     |
SIGKILL if required
     |
Pod Removed
     |
Replacement Pod
```
