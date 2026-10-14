# Class 56 – HPA and VPA
class url: https://youtu.be/8wCs4kaZccE
## 1. HPA (Horizontal Pod Autoscaler)

- HPA automatically increases or decreases the number of pod replicas based on CPU and memory utilization.
- It supports Deployments, ReplicaSets, and StatefulSets.
- We need **Metrics Server** to provide CPU and memory metrics to HPA.

### Install Metrics Server

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

If your lab's kubelets use certificates that Metrics Server cannot validate, you can add the following argument to the Metrics Server deployment:

```bash
kubectl edit deployment -n kube-system metrics-server
```

Add under the container's `args`:

```yaml
- --kubelet-insecure-tls
```

**Note:** This disables kubelet TLS certificate verification. Use it only in a lab environment, not in production.

### Verify Metrics Server

```bash
kubectl get pods -n kube-system
kubectl top nodes
kubectl top pods
```

### HPA Example

Create an HPA that maintains 50% average CPU utilization:

```bash
kubectl autoscale deployment nginx \
  --cpu-percent=50 \
  --min=2 \
  --max=10
```

Check HPA status:

```bash
kubectl get hpa
kubectl describe hpa
```

**How HPA works:**

- If CPU utilization increases, HPA can increase the number of pods.
- If CPU utilization decreases, HPA can reduce the number of pods.
- `minReplicas` defines the minimum number of pods.
- `maxReplicas` defines the maximum number of pods.

---

## 2. VPA (Vertical Pod Autoscaler)

- VPA automatically adjusts CPU and memory requests for containers based on their resource usage.
- It helps allocate suitable resources to pods.
- VPA requires Metrics Server for resource metrics.
- VPA is a separate component and must be installed independently.

### VPA Components

1. **Recommender** – Recommends CPU and memory requests.
2. **Updater** – Evicts pods when necessary so they can be recreated with updated resource requests.
3. **Admission Controller** – Applies recommended resource requests when pods are created.

### VPA Update Modes

- **Off** – Provides recommendations only; does not apply them automatically.
- **Initial** – Applies recommendations when pods are created.
- **Recreation** – Updates resource requests by recreating pods when needed.
- **InPlaceOrRecreate** – Attempts to update resources in place where supported; otherwise, it may recreate pods.

### HPA vs VPA

| HPA | VPA |
|---|---|
| Horizontal Pod Autoscaler | Vertical Pod Autoscaler |
| Increases or decreases pod replicas | Adjusts CPU and memory requests |
| Scales the number of pods | Changes resources allocated to containers |
| Useful for handling increased traffic | Useful for optimizing resource allocation |

### Important Note

Avoid using HPA CPU or memory utilization targets together with VPA changing the same resource requests, because VPA changes can affect HPA calculations. They can be combined carefully when configured for different metrics or resource dimensions.
