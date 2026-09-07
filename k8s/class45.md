# Class 45 - Kubernetes ConfigMap

class url: https://youtu.be/n9hp5nvRNLM
## What is ConfigMap?

* ConfigMap stores **non-sensitive configuration data**.
* It separates configuration from application code.
* Same Docker image can be used in different environments.
* Examples: database URL, application environment, API URL, upload path.

> Use **Secrets** for passwords and sensitive data.

---

## 1. Create ConfigMap using Command Line

```bash
kubectl create configmap my-config \
  --from-literal=APP_NAME=email-service \
  --from-literal=ENVIRONMENT=production
```

---

## 2. Create ConfigMap using YAML

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config

data:
  APP_NAME: myapp
  ENVIRONMENT: production
```

```bash
kubectl apply -f configmap.yaml
```

---

## 3. Create ConfigMap from File

### Using `--from-file`

```bash
kubectl create configmap app-config \
  --from-file=app.properties
```

The complete file content is stored as **one key**.

### Using `--from-env-file`

```bash
kubectl create configmap app-config \
  --from-env-file=.env
```

Each `KEY=VALUE` becomes a **separate key**.

### Difference

```text
--from-file       → Complete file = one key
--from-env-file   → Each KEY=VALUE = separate key
```

`.env` is **not mandatory**. Any suitable filename can be used with `--from-env-file`.

---

# Inject ConfigMap into Pod

## 1. Individual Key

Use `env` with `configMapKeyRef`.

```yaml
env:
  - name: APP_NAME
    valueFrom:
      configMapKeyRef:
        name: my-config
        key: APP_NAME
```

## 2. All Keys

Use `envFrom`.

```yaml
envFrom:
  - configMapRef:
      name: my-config
```

### Remember

```text
env     → Individual key
envFrom → All keys
```

---

# ConfigMap with Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app

spec:
  replicas: 2

  selector:
    matchLabels:
      app: my-app

  template:
    metadata:
      labels:
        app: my-app

    spec:
      containers:
        - name: app
          image: nginx:latest

          envFrom:
            - configMapRef:
                name: my-config
```

---

# Useful Commands

```bash
kubectl get configmap
```

```bash
kubectl get configmap my-config -o yaml
```

```bash
kubectl describe configmap my-config
```

```bash
kubectl delete configmap my-config
```

### Restart Deployment after ConfigMap change

```bash
kubectl rollout restart deployment my-app
```

> Updating a ConfigMap does **not automatically restart Pods** when its values are injected as environment variables.
