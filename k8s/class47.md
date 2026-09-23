# Class 47 - Kubernetes Container Types
class url: https://youtu.be/G-MUAD-etvY
## Multi-Container Pod Patterns

* A Pod can contain multiple containers.
* Usually, one container runs the **main application**.
* Other containers support or extend the functionality of the main application.

Main patterns:

1. Init Container
2. Sidecar Container
3. Adapter Container
4. Ambassador Container

---

# 1. Init Container

* Init containers run **before the main application container**.
* They must complete successfully before the main container starts.
* They are mainly used for **initialization tasks**.
* Init containers are **Pod-level objects**.
* Init containers do not use the normal application probes.
* They can also be used to delay the startup of the main container.

### Use Cases

* Install required software.
* Prepare files or directories.
* Set file permissions.
* Perform database initialization.
* Wait for a prerequisite service.
* Delay application startup.

### Example

```yaml
initContainers:
  - name: init-container
    image: busybox
    command: ["/bin/sh"]
    args:
      - "-c"
      - "echo Initialization started; sleep 30; echo Initialization completed"
```

### Demo

Apply the YAML:

```bash
kubectl apply -f init_container.yml
```

Login to the Pod:

```bash
kubectl exec -it <pod_name> -- /bin/sh
```

Install curl:

```bash
apt update
apt install -y curl
```

Test the application:

```bash
curl localhost
```

### Check Logs of a Specific Container

```bash
kubectl logs <pod_name> -c <container_name>
```

---

# 2. Sidecar Container

* A sidecar container runs **along with the main application container**.
* It is used to extend the functionality of an existing application.
* We can add functionality without changing the main application code.

### Use Cases

* Log collection and forwarding.
* Monitoring.
* Synchronizing files with a Git repository.
* Sending logs to an external server.
* Network-related tasks.
* Supporting the main application.

Example:

```text
+---------------------------+
|           Pod             |
|                           |
|  +---------------------+  |
|  |  Main Application   |  |
|  +---------------------+  |
|             |             |
|  +---------------------+  |
|  |  Sidecar Container  |  |
|  +---------------------+  |
+---------------------------+
```

---

# 3. Adapter Container

* An Adapter container is used to **transform or adapt** the output of the main application.
* It helps applications communicate with systems that expect a different format or protocol.

### Use Cases

* Data transformation.
* Protocol conversion.
* Metrics conversion.
* Log format conversion.
* Legacy application integration.

### Examples

```text
Application → Adapter → Monitoring Tool
Application → Adapter → Log System
Application → Adapter → Different Protocol
```

Example:

```text
XML → Adapter → JSON
HTTP → Adapter → gRPC
```

### Linux Example

To continuously display a file that is being updated:

```bash
tail -f <file_name>
```

---

# 4. Ambassador Container

* An Ambassador container acts as a **proxy** between the main application and an external service.
* It handles communication between the application and the external service.
* It can hide the details of the external service from the main application.

### Use Cases

* Service discovery.
* Load balancing.
* Retry failed requests.
* Authentication and authorization.
* Security policies.
* Communication with external services.

Example:

```text
+---------------------------+
|           Pod             |
|                           |
|  Main App → Ambassador    |
|                 |         |
+-----------------|---------+
                  |
                  v
           External Service
```

### Example

```text
Application → Ambassador → External Database
Application → Ambassador → External API
```

The application communicates with the Ambassador, and the Ambassador manages communication with the external service.
