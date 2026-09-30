# Class 52 – Kubernetes Probes
class url: https://youtu.be/-R0l-uEa19c

## Probes

* Probes are periodic checks performed by the **Kubelet** to determine the health or availability of a container.
* Probes are used to check whether an application inside a container is working correctly.
* We can configure how many consecutive failures are required before considering a probe failed.
* We can also configure how many consecutive successes are required before considering a probe successful.
* Probes work at the **container level**.
* **Init containers do not support probes.**

---

## Common Probe Fields

### `initialDelaySeconds`

* Number of seconds to wait after the container starts before the first probe is performed.

```yaml
initialDelaySeconds: 10
```

### `periodSeconds`

* Number of seconds between consecutive probe checks.
* Default: **10 seconds**
* Minimum: **1 second**

```yaml
periodSeconds: 10
```

### `timeoutSeconds`

* Number of seconds to wait for the probe response.
* If the probe does not respond within this time, it is considered a failure.
* Default: **1 second**

```yaml
timeoutSeconds: 2
```

### `failureThreshold`

* Number of consecutive failures required to consider the probe failed.
* Default: **3**
* Minimum: **1**

```yaml
failureThreshold: 3
```

### `successThreshold`

* Number of consecutive successful checks required to consider the probe successful.
* Default: **1**
* Minimum: **1**

```yaml
successThreshold: 1
```

---

# Probe Types

Kubernetes supports three main types of probes:

1. **HTTP Probe**
2. **TCP Probe**
3. **Exec Probe**

---

## 1. HTTP Probe

HTTP probes use an HTTP request to check an application endpoint.

```yaml
httpGet:
  path: /health
  port: 8080
```

### HTTP Probe Fields

**`host`**

* Hostname to connect to.
* If not specified, the request is sent to the **Pod IP address**.

```yaml
host: www.google.com
```

**`path`**

* HTTP path that should be accessed.

```yaml
path: /health
```

**`httpHeaders`**

* Used to send custom HTTP headers with the request.

```yaml
httpHeaders:
  - name: Custom-Header
    value: my-value
```

**`port`**

* Port on which the application is listening.
* Can be specified using a port number or named port.

```yaml
port: 8080
```

---

## 2. TCP Probe

TCP probes check whether a TCP connection can be established to the specified port.

```yaml
tcpSocket:
  port: 8080
```

### TCP Probe Field

**`port`**

* Port on which the application is listening.
* Can be a port number or named port.

---

## 3. Exec Probe

Exec probes execute a command inside the container.

```yaml
exec:
  command:
    - cat
    - /tmp/healthy
```

* If the command exits with **status code 0**, the probe is successful.
* A **non-zero exit code** indicates failure.

---

# Liveness Probe

* A **liveness probe** checks whether the application inside the container is still healthy.
* If the liveness probe fails continuously according to the configured `failureThreshold`, the **Kubelet restarts the container**.
* It is useful when an application is running but has become stuck or unhealthy.

### Example

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 10
  timeoutSeconds: 2
  failureThreshold: 3
```

### Flow

```text
Application
     |
     v
Liveness Probe
     |
     +---- Healthy ----> Continue running
     |
     +---- Failed ------> Kubelet restarts container
```

---

# Readiness Probe

* A **readiness probe** checks whether the application is ready to receive traffic.
* If the readiness probe fails, Kubernetes removes the Pod from the **Service endpoints**.
* The container is **not restarted** just because the readiness probe fails.
* Once the application becomes ready again, the Pod can receive traffic again.

### Example

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
  timeoutSeconds: 2
  failureThreshold: 3
```

### Flow

```text
Application
     |
     v
Readiness Probe
     |
     +---- Ready -------> Service sends traffic
     |
     +---- Not Ready ---> Service stops sending traffic
```

---

# Startup Probe

* A **startup probe** is used for applications that take a long time to start.
* When a startup probe is configured:

  * Kubernetes waits for the startup probe to succeed.
  * **Liveness and readiness probes are not executed until the startup probe succeeds.**
* This prevents a slow-starting application from being restarted by the liveness probe before it has finished starting.
* Once the startup probe succeeds, normal liveness and readiness checks begin.

### Example

```yaml
startupProbe:
  httpGet:
    path: /health
    port: 8080
  periodSeconds: 10
  failureThreshold: 30
```

In this example:

```text
periodSeconds × failureThreshold
= 10 × 30
= 300 seconds
= 5 minutes
```

So the application gets up to approximately **5 minutes** for the startup probe to succeed.

---

# Liveness vs Readiness vs Startup

| Probe         | Purpose                                      | If Probe Fails                     |
| ------------- | -------------------------------------------- | ---------------------------------- |
| **Liveness**  | Is the application still healthy?            | Container may be restarted         |
| **Readiness** | Is the application ready to receive traffic? | Pod removed from Service endpoints |
| **Startup**   | Has the application finished starting?       | Liveness/Readiness remain disabled |

---

## Important Point

If the application starts slowly, simply increasing `initialDelaySeconds` can work in some cases, but it has a limitation.

For example:

```yaml
initialDelaySeconds: 120
```

Kubernetes waits 120 seconds before the first liveness check.

But if the application sometimes takes **30 seconds** and sometimes **5 minutes** to start, a fixed `initialDelaySeconds` is not ideal.

A **startup probe** is better for slow or unpredictable application startup because it gives the application a defined amount of time to become healthy before liveness checking begins.

---

# Complete Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp

  template:
    metadata:
      labels:
        app: myapp

    spec:
      containers:
        - name: myapp
          image: nginx:latest
          ports:
            - containerPort: 80

          startupProbe:
            httpGet:
              path: /
              port: 80
            periodSeconds: 10
            failureThreshold: 30

          livenessProbe:
            httpGet:
              path: /
              port: 80
            periodSeconds: 10
            failureThreshold: 3

          readinessProbe:
            httpGet:
              path: /
              port: 80
            periodSeconds: 10
            failureThreshold: 3
```

### Probe Execution

```text
Container starts
       |
       v
Startup Probe
       |
       |-- Failure --> Keep waiting
       |
       |-- Success
       v
Liveness + Readiness Probes start
       |
       +------------------+
       |                  |
       v                  v
Liveness              Readiness
   |                      |
   v                      v
Healthy?              Ready?
   |                      |
   v                      v
Continue             Receive traffic
```

## Key Points

* **Liveness → Restart the container when the application is unhealthy.**
* **Readiness → Control whether the Pod receives traffic.**
* **Startup → Protect slow-starting applications.**
* Startup probe succeeds first; then liveness and readiness probes start.
* Probes are configured for containers, not init containers.
