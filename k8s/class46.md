# Class 46 - StatefulSet, Headless Service and Secrets
class url: https://youtu.be/9qHio9nDnu4
## StatefulSet

* **StatefulSet** is used to manage **stateful applications** such as databases.
* It provides:

  * Stable pod names
  * Stable network identity
  * Stable storage
  * Ordered pod creation and deletion

### Deployment vs StatefulSet

| Deployment                                  | StatefulSet                            |
| ------------------------------------------- | -------------------------------------- |
| Pod names are not fixed                     | Pod names are fixed                    |
| Pods are interchangeable                    | Each pod has a unique identity         |
| Mainly used for stateless applications      | Mainly used for stateful applications  |
| No ordered pod creation                     | Pods are created in order              |
| Storage is not automatically unique per pod | Can create a separate PVC for each pod |

## 1. Stable Pod Identity

Each StatefulSet pod gets a unique name:

```text
my-statefulset-0
my-statefulset-1
my-statefulset-2
```

* The pod name remains the same even if the pod is deleted and recreated.
* Each pod gets an **ordinal index** starting from `0`.

### Scaling

When scaling up:

```text
my-statefulset-0
my-statefulset-1
my-statefulset-2
```

Pods are created in order.

When scaling down, pods are removed in reverse order:

```text
my-statefulset-2
my-statefulset-1
my-statefulset-0
```

## 2. Stable Network Identity

Each StatefulSet pod can have a stable DNS identity when used with a **Headless Service**.

Example:

```text
my-statefulset-0.headless-svc.default.svc.cluster.local
my-statefulset-1.headless-svc.default.svc.cluster.local
```

## 3. Stable Storage

StatefulSet can create a separate **PVC** for each pod.

Example:

```text
my-statefulset-0 → PVC → PV
my-statefulset-1 → PVC → PV
my-statefulset-2 → PVC → PV
```

If `my-statefulset-0` is deleted and recreated, its PVC can be reused.

---

# Headless Service

A **Headless Service** does not have a ClusterIP.

It is created using:

```yaml
spec:
  clusterIP: None
```

### Why use Headless Service?

* No single ClusterIP is provided.
* DNS can return the IP addresses of the selected pods.
* Commonly used with StatefulSets.
* It provides direct DNS-based access to individual StatefulSet pods.

### Example

```yaml
apiVersion: v1
kind: Service
metadata:
  name: headless-svc
spec:
  clusterIP: None
  selector:
    app: nginx-s
  ports:
    - port: 80
      targetPort: 80
```

### Demo

1. Create the Headless Service and StatefulSet.

2. Login to a pod:

```bash
kubectl exec -it <pod_name> -- /bin/bash
```

3. Install DNS utilities:

```bash
apt update
apt install dnsutils
```

4. Check the service:

```bash
nslookup headless-svc
```

You can also check an individual StatefulSet pod:

```bash
nslookup my-statefulset-0.headless-svc.default.svc.cluster.local
```

---

# Secrets

**Secrets** are Kubernetes resources used to store sensitive information.

Examples:

* Passwords
* API keys
* TLS certificates
* Docker registry credentials
* SSH keys

Secrets are different from **ConfigMaps**, which are normally used for non-sensitive configuration.

> Kubernetes Secret values are base64-encoded by default. Base64 encoding is not encryption.

## Types of Secrets

1. **Opaque** - Generic key-value data
2. **kubernetes.io/service-account-token** - Service account tokens
3. **kubernetes.io/tls** - TLS certificate and key
4. **kubernetes.io/dockerconfigjson** - Docker registry credentials
5. **kubernetes.io/basic-auth** - Basic authentication credentials
6. **kubernetes.io/ssh-auth** - SSH credentials

---

# Create Secrets

## 1. From Literal Values

```bash
kubectl create secret generic my-secret \
  --from-literal=username=admin \
  --from-literal=password=password
```

## 2. From a Single File

```bash
kubectl create secret generic my-secret-file \
  --from-file=./password.txt
```

## 3. From Multiple Files

```bash
kubectl create secret generic my-secret \
  --from-file=./username.txt \
  --from-file=./password.txt
```

Example:

```bash
echo -n "password" > password.txt
echo -n "admin" > username.txt
```

`-n` prevents adding a newline character.

## 4. From `.env` File

```bash
kubectl create secret generic my-secret \
  --from-env-file=.env
```

## 5. From YAML

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: YWRtaW4=
  password: cGFzc3dvcmQ=
```

Decode a value:

```bash
echo "YWRtaW4=" | base64 --decode
```

---

# Inject Secrets into Pods

## 1. Inject Individual Keys

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod-with-secrets
spec:
  containers:
    - name: my-container
      image: harshajain/ip_app:latest
      env:
        - name: USERNAME
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: username

        - name: PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: password
```

Here, specific keys are selected using:

```yaml
secretKeyRef:
```

## 2. Inject All Secret Keys

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod-with-secrets
spec:
  containers:
    - name: my-container
      image: harshajain/ip_app:latest
      envFrom:
        - secretRef:
            name: my-secret
```

`envFrom` loads **all keys** from the Secret as environment variables.

## Important Commands

```bash
kubectl get secrets
```

```bash
kubectl describe secret my-secret
```

```bash
kubectl get secret my-secret -o yaml
```
