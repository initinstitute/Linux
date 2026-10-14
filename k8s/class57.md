# Class 57 – Network Policy
class url: https://youtu.be/bkZEwyTjww8
## 1. What is a Network Policy?

- Network Policies control incoming and outgoing network traffic between Kubernetes Pods.
- They help prevent unnecessary communication between applications.
- They improve security by limiting the impact if an application is compromised.
- Network Policies work at OSI Layer 3 and Layer 4 (IP addresses, protocols, and ports).
- Network Policies are enforced by the CNI plugin installed in the cluster.

## 2. Important Points

- Network Policies are **additive**. If multiple policies select a Pod, the allowed traffic from all applicable policies is combined.
- Traffic not allowed by any applicable policy is blocked for the traffic direction that is isolated.
- Network Policies are namespace-scoped.
- The default behavior is to allow traffic unless a policy isolates the Pod for ingress or egress.
- A Network Policy does not work unless the installed CNI plugin supports and enforces it.

## 3. Types of Network Policy Selectors

There are three common ways to identify network endpoints.

### 1. podSelector

Selects specific Pods using labels.

```yaml
podSelector:
  matchLabels:
    app: backend
```

This selects Pods with the label `app: backend` in the policy's namespace.

### 2. namespaceSelector

Selects Pods belonging to namespaces with specific labels.

```yaml
namespaceSelector:
  matchLabels:
    environment: production
```

This selects Pods in namespaces labeled `environment: production`.

### 3. ipBlock

Selects IP addresses within a specified CIDR range.

```yaml
ipBlock:
  cidr: 192.168.1.0/24
```

- Useful for allowing or denying traffic to specific IP ranges.
- Pod IP addresses can change, so `ipBlock` is generally not the best way to identify individual Pods.

## 4. Network Policy Example

Allow incoming traffic to backend Pods only from frontend Pods on TCP port 80.

Save as `network-policy.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: backend-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
    - Ingress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 80
```

Apply the policy:

```bash
kubectl apply -f network-policy.yaml
```

Check the policy:

```bash
kubectl get networkpolicy
kubectl describe networkpolicy backend-policy
```

**Explanation:**

- The policy selects Pods labeled `app: backend`.
- It allows incoming TCP traffic on port `80` from Pods labeled `app: frontend` in the same namespace.
- Other incoming traffic to the selected backend Pods is blocked, unless another applicable policy allows it.
- Outgoing traffic is unaffected because this policy isolates only ingress traffic.

## 5. Port Range in Network Policy

You can specify a range of ports using `endPort`.

```yaml
ports:
  - protocol: TCP
    port: 32000
    endPort: 32100
```

This allows TCP ports from `32000` through `32100`, inclusive, when used in an applicable ingress or egress rule.

## 6. Network Policy Limitations

1. **No standard logging:** Kubernetes NetworkPolicy does not define a standard mechanism for logging blocked traffic. CNI plugins may provide logging or monitoring features.
2. **Namespace-scoped:** Standard NetworkPolicy objects apply within a namespace. Cluster-wide policies require additional features, such as Calico GlobalNetworkPolicy.
3. **Loopback traffic:** Traffic sent within a Pod through the loopback interface, such as `127.0.0.1`, is not controlled by NetworkPolicy.
4. **Host network traffic:** Pods using `hostNetwork: true` have special behavior, and NetworkPolicy enforcement for them is implementation-dependent. Do not rely on standard NetworkPolicy to secure host-network traffic.
5. **CNI dependency:** Network Policies are ineffective if the installed network plugin does not support them.

## 7. Host Network Pod Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hostnetwork-pod
  labels:
    app: hostnetwork-demo
spec:
  hostNetwork: true
  containers:
    - name: demo-container
      image: busybox
      command: ["sh", "-c", "echo 'Hello from host network'; sleep 3600"]
```

Apply the Pod:

```bash
kubectl apply -f hostnetwork-pod.yaml
```

Check its network interfaces:

```bash
kubectl exec -it hostnetwork-pod -- ip addr show
```

**Note:** With `hostNetwork: true`, the Pod uses the host's network namespace and can share the host's IP address.

## 8. CNI Support

- **Calico:** Supports standard NetworkPolicy and provides additional features such as GlobalNetworkPolicy.
- **Flannel:** The basic Flannel plugin does not enforce Kubernetes NetworkPolicies by itself.
- **Cloud-managed Kubernetes:** NetworkPolicy support depends on the selected CNI, configuration, and cluster setup. Verify that enforcement is enabled.

## 9. Verify Network Policies

```bash
kubectl get pods --show-labels
kubectl get namespaces --show-labels
kubectl get networkpolicy -A
kubectl describe networkpolicy backend-policy
```

## Summary

- `podSelector` selects Pods using labels.
- `namespaceSelector` selects namespaces using labels.
- `ipBlock` selects IP address ranges.
- `Ingress` controls incoming traffic.
- `Egress` controls outgoing traffic.
- Network Policies are additive and require CNI support.
- Use Network Policies to implement least-privilege network access between applications.
