# Class 55 – ResourceQuota, Requests, Limits and IRSA
class url (Sound not Available): https://youtu.be/Xmwf2lRaUtI
## 1. ResourceQuota

ResourceQuota is used to limit the number of Kubernetes resources in a namespace.

**Common ResourceQuota resources:**

* `count/pods`
* `count/persistentvolumeclaims`
* `count/services`
* `count/secrets`
* `count/configmaps`
* `count/replicationcontrollers`
* `count/deployments.apps`
* `count/replicasets.apps`
* `count/statefulsets.apps`
* `count/jobs.batch`
* `count/cronjobs.batch`

**ResourceQuota YAML (`rc.yaml`)**

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: pod-count-quota
  namespace: default
spec:
  hard:
    count/pods: "2"
    count/deployments.apps: "2"
```

**Commands:**

```bash
kubectl apply -f rc.yaml
kubectl get resourcequota
kubectl describe resourcequota pod-count-quota
```

This quota allows a maximum of 2 Pods and 2 Deployment objects in the specified namespace.

## 2. CPU and Memory Resources

Kubernetes allows us to configure resource requests and limits for containers.

**Pod YAML (`pod.yaml`)**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - name: nginx
      image: nginx:1.14.2
      ports:
        - containerPort: 80
      resources:
        requests:
          memory: "100Mi"
          cpu: "500m"
        limits:
          memory: "250Mi"
          cpu: "1000m"
```

### CPU

* `1 CPU` = one logical CPU core.
* `1000m` = 1 CPU.
* `500m` = 0.5 CPU.
* `100m` = 0.1 CPU.
* CPU requests and limits are not automatically set to 0.5 CPU; they depend on the configuration and any applicable admission policies.

### Memory

Memory is measured in bytes.

Common units:

* `Ki` – Kibibyte
* `Mi` – Mebibyte
* `Gi` – Gibibyte
* `Ti` – Tebibyte

Examples: `100Mi`, `512Mi`, `1Gi`.

## 3. Requests

Requests define the amount of CPU and memory Kubernetes uses for scheduling a container.

* The scheduler uses requests to select a suitable node.
* CPU requests do not dedicate an entire CPU core.
* Memory requests are used for scheduling and do not guarantee exclusive physical memory.
* A Pod remains Pending if no suitable node has sufficient allocatable resources.

## 4. Limits

Limits define the maximum resources a container can use.

* **CPU limit:** CPU usage is throttled when the limit is reached.
* **Memory limit:** If the container exceeds its memory limit and the system cannot reclaim enough memory, it may be terminated with `OOMKilled`.
* Limits help prevent a container from consuming excessive resources.

## 5. Requests vs Limits

| Resource | Requests             | Limits                |
| -------- | -------------------- | --------------------- |
| CPU      | Used for scheduling  | CPU throttling        |
| Memory   | Used for scheduling  | May trigger OOM kill  |
| Purpose  | Resource requirement | Maximum allowed usage |

**Example:**

```yaml
resources:
  requests:
    cpu: "500m"
    memory: "100Mi"
  limits:
    cpu: "1000m"
    memory: "250Mi"
```

The container requests 0.5 CPU and 100 MiB of memory. Its configured maximum is 1 CPU and 250 MiB of memory.

## 6. IRSA (IAM Roles for Service Accounts)

IRSA allows applications running in Amazon EKS to access AWS services using an IAM role instead of storing AWS access keys in the application.

**Example:** A Pod needs permission to access an S3 bucket.

### Steps to configure IRSA

1. Enable the IAM OIDC provider for the EKS cluster.
2. Create an IAM policy with the required AWS permissions.
3. Create an IAM role with a trust policy for the Kubernetes ServiceAccount.
4. Attach the IAM policy to the role.
5. Create a Kubernetes ServiceAccount with the IAM role annotation.
6. Use that ServiceAccount in the Pod.

### ServiceAccount YAML (`irsa-sa.yaml`)

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: s3-sa
  namespace: default
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::123456789012:role/S3AccessRole
```

Replace the account ID and role name with your actual AWS values.

**Apply the ServiceAccount:**

```bash
kubectl apply -f irsa-sa.yaml
```

### Pod YAML (`s3-pod.yaml`)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: s3-pod
spec:
  serviceAccountName: s3-sa
  containers:
    - name: aws-cli
      image: amazon/aws-cli
      command: ["sleep", "3600"]
```

**Verify AWS identity:**

```bash
kubectl exec -it s3-pod -- aws sts get-caller-identity
```

### Important points

* IRSA is used with Amazon EKS.
* The IAM role trust policy must allow the correct OIDC provider, ServiceAccount namespace, and ServiceAccount name.
* Attach only the AWS permissions the application needs.
* The annotation alone is not sufficient; the OIDC provider, IAM role, and trust policy must also be configured.

## 7. IRSA vs Kubernetes RBAC

| IRSA                            | RBAC                                        |
| ------------------------------- | ------------------------------------------- |
| Controls access to AWS services | Controls access to Kubernetes API resources |
| Uses IAM roles and policies     | Uses Roles, ClusterRoles and bindings       |
| Example: access an S3 bucket    | Example: list Pods in a namespace           |

**Remember:** IRSA grants AWS permissions; RBAC grants Kubernetes API permissions.
