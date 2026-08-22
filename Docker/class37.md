# Class 37 – Virtualization, Containers & Docker Basics

## 1. Virtualization

**Virtualization** is the process of creating virtual computers inside a physical computer.

For example, one physical server can run multiple virtual machines:

```text
Physical Server
      |
   Hypervisor
      |
  +---+---+---+
  |   |   |   |
 VM1 VM2 VM3 VM4
```

Each VM can have its own operating system.

---

## 2. Hypervisor

A **Hypervisor** is software that creates and manages Virtual Machines.

It divides the physical server resources such as:

* CPU
* RAM
* Storage
* Network

### Example

```text
Physical Server
      |
  Hypervisor
      |
  +---+---+
  |       |
 VM1     VM2
Ubuntu  Windows
```

### Types of Hypervisors

**Type 1:** Runs directly on physical hardware.

Examples:

* VMware ESXi
* Microsoft Hyper-V
* KVM

**Type 2:** Runs on top of an operating system.

Examples:

* VirtualBox
* VMware Workstation

---

## 3. Containerization

**Containerization** is a way of packaging an application along with its required libraries and dependencies.

A container is lightweight because it shares the host operating system kernel.

```text
Host Operating System
        |
  Container Engine
        |
   +----+----+
   |         |
Container  Container
   |         |
  App       App
```

---

## 4. Virtual Machine vs Container

```text
Virtual Machine
      |
 Hypervisor
      |
    VM
      |
 Full OS
      |
 Application
```

```text
Container
      |
Container Engine
      |
 Container
      |
 Application
```

### Main Difference

* **VM** → Contains a complete operating system.
* **Container** → Shares the host OS kernel.
* **VMs** are generally heavier.
* **Containers** are generally lightweight and start faster.

---

# 5. Docker

**Docker** is a platform used to create, run, and manage containers.

Docker helps us package an application and run it consistently in different environments.

Example:

```text
Application
     +
Dependencies
     |
     ▼
Docker Image
     |
     ▼
Docker Container
```

---

# 6. Docker Architecture

Docker mainly follows a client-server architecture.

```text
Docker Client
      |
      ▼
Docker Daemon
      |
  +---+---+---+
  |   |   |   |
Images Containers Networks Volumes
```

---

# 7. Docker Components

### Docker Client

The Docker Client is where we run Docker commands.

Example:

```bash
docker ps
docker images
docker run nginx
```

### Docker Daemon

Docker Daemon runs in the background and manages:

* Containers
* Images
* Networks
* Volumes

The daemon process is called:

```text
dockerd
```

### Docker Image

An **Image** is a template used to create containers.

Example:

```text
nginx image
     |
     +---- Container 1
     |
     +---- Container 2
```

### Docker Container

A **Container** is a running instance of a Docker image.

```bash
docker run nginx
```

### Docker Registry

A **Docker Registry** stores Docker images.

Example:

**Docker Hub** is a commonly used public Docker registry.

```text
Docker Hub
    |
    | docker pull
    ▼
Docker Host
    |
 Docker Image
    |
 Container
```

### Docker Volume

A **Volume** is used to store data permanently outside the container's writable layer.

```text
Container
    |
    ▼
Docker Volume
    |
Persistent Data
```

### Docker Network

A **Docker Network** allows containers to communicate with each other.

```text
Frontend Container
        |
   Docker Network
        |
Backend Container
        |
   Docker Network
        |
Database Container
```

---

# 8. Docker Basic Flow

The basic Docker flow is:

```text
Dockerfile
    |
    | docker build
    ▼
Docker Image
    |
    | docker run
    ▼
Docker Container
```

### Important Terms

| Term             | Meaning                                |
| ---------------- | -------------------------------------- |
| Virtualization   | Creating virtual machines              |
| Hypervisor       | Manages virtual machines               |
| Containerization | Packaging applications into containers |
| Docker           | Container platform                     |
| Image            | Template for creating containers       |
| Container        | Running instance of an image           |
| Dockerfile       | Instructions to build an image         |
| Registry         | Stores Docker images                   |
| Volume           | Persistent container storage           |
| Network          | Enables container communication        |

