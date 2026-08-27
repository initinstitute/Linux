# Class 39 - Introduction to Kubernetes
class link : https://youtu.be/b8eQeTOD3eo
## 1. Introduction

**Kubernetes (K8s)** is an open-source container orchestration platform used to deploy, manage, scale, and maintain containerized applications.

Before understanding Kubernetes, it is important to understand how application deployment evolved from traditional deployment to containers and finally to Kubernetes.

---

# 2. Application Deployment Evolution

The basic evolution is:

```text
Application Code
      |
      v
   JAR / WAR
      |
      v
    Docker
      |
      v
 Kubernetes
```

A more detailed view:

```text
Developer Code
      |
      v
Java Application
      |
      v
JAR File
      |
      v
Application Server / JVM
      |
      v
Docker Image
      |
      v
Docker Container
      |
      v
Kubernetes
      |
      v
Pods
      |
      v
Production Application
```

---

# 3. Traditional Application Deployment

In traditional deployment, developers build the application and generate an executable package.

For a Java application, the output can be:

```text
application.jar
```

For a web application:

```text
application.war
```

Example:

```bash
mvn clean package
```

After successful build:

```text
target/
└── application.jar
```

The JAR file can then be copied to a server.

---

# 4. Running a JAR File

A Java JAR application can be started using:

```bash
java -jar application.jar
```

Example:

```bash
java -jar calculator-1.0.jar
```

The application runs using the Java Runtime Environment.

Example:

```text
EC2 / Linux Server
       |
       +-- Java
       |
       +-- application.jar
```

### Problems with Traditional Deployment

Some common problems are:

* Java version differences
* Dependency issues
* Operating system differences
* Configuration differences
* Manual deployment
* Difficult scaling
* Environment inconsistencies

For example:

```text
Developer Environment
Java 21
   |
   v
Production Server
Java 17
   |
   v
Application Issue
```

---

# 5. What is Docker?

**Docker** is a containerization platform.

Docker packages the application along with the required dependencies and runtime environment into a container image.

Instead of deploying only:

```text
application.jar
```

we can package:

```text
Application
+
Java Runtime
+
Dependencies
+
Configuration
```

into a Docker image.

---

# 6. JAR to Docker

Suppose we have:

```text
application.jar
```

We create a Dockerfile.

Example:

```dockerfile
FROM eclipse-temurin:21-jre

COPY application.jar /app/application.jar

WORKDIR /app

EXPOSE 8080

CMD ["java", "-jar", "application.jar"]
```

Build the Docker image:

```bash
docker build -t my-java-app:1.0 .
```

Check the image:

```bash
docker images
```

Run the container:

```bash
docker run -d -p 8080:8080 my-java-app:1.0
```

Now the application is running inside a container.

---

# 7. Docker Image

A Docker image is a package containing everything required to run an application.

Example:

```text
Docker Image
   |
   +-- Application JAR
   +-- Java Runtime
   +-- Libraries
   +-- Configuration
   +-- Base OS files
```

Docker image example:

```text
my-java-app:1.0
```

---

# 8. Docker Container

A container is a running instance of a Docker image.

```text
Docker Image
     |
     v
Docker Container
     |
     v
Java Application
```

Example:

```bash
docker run -d -p 8080:8080 my-java-app:1.0
```

Check running containers:

```bash
docker ps
```

---

# 9. Problems with Docker Alone

Docker solves many application packaging problems, but running containers manually becomes difficult when the number of containers increases.

For example:

```text
Application
    |
    +-- Container 1
    +-- Container 2
    +-- Container 3
    +-- Container 4
    +-- Container 5
```

Managing many containers manually becomes difficult.

Problems include:

* Container failure
* Scaling
* Load balancing
* Service discovery
* Rolling updates
* Rollbacks
* Networking
* Configuration management
* High availability

This is where Kubernetes becomes useful.

---

# 10. What is Kubernetes?

**Kubernetes** is a container orchestration platform.

It helps us manage containers automatically.

Kubernetes can:

* Deploy containers
* Create multiple replicas
* Restart failed containers
* Scale applications
* Provide service discovery
* Provide load balancing
* Perform rolling updates
* Perform rollbacks
* Manage application configuration
* Manage container networking

---

# 11. Docker to Kubernetes

The deployment flow becomes:

```text
Java Application
       |
       v
     JAR File
       |
       v
   Docker Image
       |
       v
 Docker Registry
       |
       v
   Kubernetes
       |
       v
      Pod
       |
       v
 Container
       |
       v
 Java Application
```

Example:

```text
application.jar
       |
       v
docker build
       |
       v
my-java-app:1.0
       |
       v
Docker Hub / ECR
       |
       v
Kubernetes
       |
       v
Pod
       |
       v
Container
```

---

# 12. Docker Registry

A Docker registry stores Docker images.

Examples:

* Docker Hub
* Amazon ECR
* GitHub Container Registry
* Azure Container Registry
* Google Artifact Registry

Example:

```text
Developer
   |
   | docker push
   v
Docker Registry
   |
   | docker pull
   v
Kubernetes Cluster
```

---

# 13. Kubernetes Cluster

A Kubernetes cluster is a collection of machines used to run containerized applications.

Basic structure:

```text
Kubernetes Cluster
        |
        +-------------------+
        |                   |
        v                   v
 Control Plane           Worker Node
                           |
                           v
                          Pod
                           |
                           v
                       Container
```

---

# 14. Control Plane

The **Control Plane** manages the Kubernetes cluster.

Important components include:

* API Server
* etcd
* Scheduler
* Controller Manager

### API Server

The API Server is the main communication point for Kubernetes.

```text
kubectl
   |
   v
API Server
```

### etcd

`etcd` stores Kubernetes cluster state and configuration.

### Scheduler

The Scheduler decides which worker node should run a new Pod.

### Controller Manager

Controllers continuously check the desired state and actual state of the cluster.

---

# 15. Worker Node

Worker nodes run the application workloads.

A worker node contains components such as:

* kubelet
* Container runtime
* kube-proxy

Example:

```text
Worker Node
    |
    +-- kubelet
    |
    +-- Container Runtime
    |
    +-- kube-proxy
    |
    +-- Pod
          |
          +-- Container
```

---

# 16. What is a Pod?

A **Pod** is the smallest deployable unit in Kubernetes.

Usually, one Pod contains one application container.

Example:

```text
Pod
 |
 +-- Container
       |
       +-- Java Application
```

Kubernetes does not directly manage Docker containers individually. Kubernetes manages **Pods**, which contain containers.

---

# 17. Kubernetes Deployment

A Deployment is used to manage application Pods.

Example:

```text
Deployment
     |
     +---- Pod 1
     |
     +---- Pod 2
     |
     +---- Pod 3
```

If we specify:

```yaml
replicas: 3
```

Kubernetes attempts to maintain three Pods.

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
Pod 4
```

Kubernetes creates a replacement Pod.

---

# 18. Scaling

Suppose our application initially has:

```text
replicas: 2
```

During high traffic, we can scale it:

```bash
kubectl scale deployment my-java-app --replicas=5
```

Now:

```text
Deployment
   |
   +-- Pod 1
   +-- Pod 2
   +-- Pod 3
   +-- Pod 4
   +-- Pod 5
```

---

# 19. Self-Healing

Kubernetes provides self-healing capabilities.

If a Pod crashes:

```text
Pod 1
  X
  |
  v
Kubernetes
  |
  v
Creates replacement Pod
```

This reduces the need for manual intervention.

---

# 20. Kubernetes Service

Pods are temporary and their IP addresses can change.

A **Service** provides a stable way to access Pods.

```text
                Service
                   |
        +----------+----------+
        |          |          |
        v          v          v
      Pod 1      Pod 2      Pod 3
```

The Service can also distribute traffic across Pods.

Common Service types:

* ClusterIP
* NodePort
* LoadBalancer

---

# 21. Complete Application Flow

A typical Java application deployment can look like this:

```text
Developer
    |
    v
Java Source Code
    |
    v
Maven Build
    |
    v
application.jar
    |
    v
Docker Build
    |
    v
Docker Image
    |
    v
Docker Registry
    |
    v
Kubernetes Cluster
    |
    v
Deployment
    |
    v
Pods
    |
    v
Containers
    |
    v
Java Application
    |
    v
Service
    |
    v
Users
```

---

# 22. Traditional Deployment vs Docker vs Kubernetes

| Feature                 | JAR Deployment  | Docker            | Kubernetes                |
| ----------------------- | --------------- | ----------------- | ------------------------- |
| Application packaging   | JAR/WAR         | Image             | Pod/Container             |
| Dependency management   | Manual          | Included in image | Managed through container |
| Scaling                 | Manual          | Manual            | Automated/managed         |
| Self-healing            | No              | Limited           | Yes                       |
| Load balancing          | External/manual | External/manual   | Service/Ingress           |
| Rolling updates         | Manual          | Manual            | Supported                 |
| Container orchestration | No              | Limited           | Yes                       |
| High availability       | Manual          | Manual            | Supported                 |

---

# 23. Important Kubernetes Terms

### Cluster

A group of machines running Kubernetes.

### Node

A machine inside the Kubernetes cluster.

### Pod

The smallest deployable unit in Kubernetes.

### Container

The application runtime inside a Pod.

### Deployment

Manages the desired number and version of Pods.

### Service

Provides stable networking and access to Pods.

### Namespace

Provides logical separation of Kubernetes resources.

### ConfigMap

Stores non-sensitive configuration.

### Secret

Stores sensitive configuration such as passwords and tokens.

---

# 24. Why Kubernetes?

Kubernetes is useful when applications need:

* High availability
* Multiple application instances
* Automatic scaling
* Self-healing
* Rolling deployments
* Rollbacks
* Service discovery
* Container orchestration

---

# 25. Key Takeaways

```text
JAR
 |
 | Application package
 v
Docker
 |
 | Packages application + runtime + dependencies
 v
Docker Image
 |
 | Stored in registry
 v
Kubernetes
 |
 | Orchestrates containers
 v
Pods
 |
 v
Application
```

### Remember

**JAR → Docker Image → Container → Kubernetes Pod → Deployment → Service**

This is the basic journey from traditional Java application deployment to modern containerized and Kubernetes-based deployment.
