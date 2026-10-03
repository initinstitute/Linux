# Class 53 – Node Scheduling in Kubernetes
class url: https://youtu.be/8HMJtT5Vgu0
## 1. NodeName

* Used to schedule a pod directly on a specific node.
* The scheduler is bypassed for node selection.
* If the specified node is unavailable, the pod remains Pending.

**Example: `pod.yaml`**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  nodeName: worker1
  containers:
    - name: nginx
      image: nginx
```

**Note:** Replace `worker1` with the actual node name.

## 2. Node Affinity

* Used to schedule pods on nodes based on their labels.
* Supports logical expressions such as `In`, `NotIn`, `Exists`, and `DoesNotExist`.
* Useful for selecting nodes based on memory, CPU, disk type, or availability zone.

### Types of Node Affinity

**1. preferredDuringSchedulingIgnoredDuringExecution**

* The scheduler tries to find a matching node.
* If no matching node is available, it can schedule the pod on another suitable node.

**2. requiredDuringSchedulingIgnoredDuringExecution**

* The node must match the specified conditions.
* If no matching node is available, the pod remains Pending.

**IgnoredDuringExecution:** If node labels change after scheduling, the running pod is not automatically removed because of that label change.

## 3. Node Anti-Affinity

* Kubernetes does not have a separate `nodeAntiAffinity` field.
* Use `nodeAffinity` with operators such as `NotIn` or `DoesNotExist` to avoid nodes with specific labels.

**Example:** Avoid nodes labelled `nodetype=memory`.

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
            - key: nodetype
              operator: NotIn
              values:
                - memory
```

## 4. Pod Affinity

* Used to schedule a pod close to other pods based on their labels.
* Uses `podAffinity`.
* Placement is controlled using a topology key, such as `kubernetes.io/hostname`.

**Example use:** Schedule application pods on the same node as their related cache pods.

## 5. Pod Anti-Affinity

* Used to avoid scheduling pods near other pods matching specified labels.
* Uses `podAntiAffinity`.
* Helps distribute application replicas across different nodes for high availability.

**Example use:** Spread frontend replicas across different worker nodes.

## 6. Taints and Tolerations

**Taints:** Applied to nodes to repel pods that do not have matching tolerations.

**Tolerations:** Added to pod specifications to allow pods to tolerate matching node taints. A toleration does not guarantee that a pod will be scheduled on that node.

### Taint Effects

1. **NoSchedule**

   * New pods without a matching toleration will not be scheduled on the node.

2. **PreferNoSchedule**

   * Kubernetes tries to avoid scheduling pods without a matching toleration on the node.

3. **NoExecute**

   * Pods without a matching toleration are evicted from the node.
   * New pods without a matching toleration are not scheduled there.

### Apply a Taint

```bash
kubectl taint node worker1 nodetype=memory:NoSchedule
```

### Remove a Taint

```bash
kubectl taint node worker1 nodetype=memory:NoSchedule-
```

### Add a Toleration to a Pod

```yaml
spec:
  tolerations:
    - key: nodetype
      operator: Equal
      value: memory
      effect: NoSchedule
```

**Note:**

* `Equal` requires a matching `value`.
* `Exists` does not require a `value`.

## 7. Node Cordon

* Prevents new ordinary pods from being scheduled on a node.
* Existing pods continue running.
* DaemonSet pods may still be scheduled on a cordoned node.

### Cordon a Node

```bash
kubectl cordon worker1
```

### Uncordon a Node

```bash
kubectl uncordon worker1
```

## 8. Node Drain

* Safely evicts eligible pods from a node for maintenance.
* The node is marked unschedulable.
* Workload controllers, such as Deployments, can create replacement pods on other suitable nodes.
* PodDisruptionBudgets can restrict voluntary evictions.

### Drain a Node

```bash
kubectl drain worker1 --ignore-daemonsets
```

### Important Difference

| Command            | Purpose                                     |
| ------------------ | ------------------------------------------- |
| `kubectl cordon`   | Prevents new pods from scheduling on a node |
| `kubectl uncordon` | Allows scheduling on a node again           |
| `kubectl drain`    | Evicts eligible pods for maintenance        |
| `kubectl taint`    | Repels pods without matching tolerations    |
