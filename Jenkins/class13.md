# 📘 Class 13 Notes – Jenkins and Jenkins Architecture

## What is Jenkins?

**Jenkins** is an open-source automation server used to automate software development tasks such as:

- Building applications
- Running tests
- Deploying applications
- Continuous Integration (CI)
- Continuous Delivery/Deployment (CD)

Jenkins helps developers automate repetitive tasks, reducing manual effort and improving software quality.

### Definition

> Jenkins is a Java-based automation tool that enables Continuous Integration (CI) and Continuous Delivery (CD) by automating the build, test, and deployment process.

---

## Why Use Jenkins?

Before Jenkins:

- Developers manually build applications.
- Testing is done manually.
- Deployments take more time.
- Higher chance of human errors.

With Jenkins:

- Automatic builds after code changes.
- Automated testing.
- Faster deployments.
- Quick feedback on code issues.
- Improved productivity.

---

## Key Features of Jenkins

### 1. Open Source

- Free to use.
- Large community support.

### 2. Platform Independent

- Runs on Linux, Windows, and macOS.

### 3. Plugin Support

- Thousands of plugins available.
- Integrates with Git, Docker, Kubernetes, AWS, Maven, etc.

### 4. Easy Integration

- Connects with version control systems like Git and GitHub.

### 5. Automation

- Automates Build, Test, and Deployment processes.

### 6. Distributed Builds

- Can run jobs on multiple machines simultaneously.

---

# Continuous Integration (CI)

Continuous Integration is the practice of frequently merging code changes into a shared repository and automatically testing them.

### CI Workflow

```text
Developer
    ↓
Git Repository
    ↓
Jenkins Build
    ↓
Automated Testing
    ↓
Build Status
```

### Benefits

- Detect bugs early.
- Faster development.
- Better collaboration among team members.

---

# Continuous Delivery (CD)

Continuous Delivery ensures that applications are always ready for deployment.

### CD Workflow

```text
Build
  ↓
Test
  ↓
Package
  ↓
Deploy
```

### Benefits

- Faster releases.
- Reliable deployments.
- Reduced manual work.

---

# Jenkins Architecture

Jenkins follows a **Master-Agent (Controller-Agent)** architecture.

## Main Components

1. Jenkins Controller (Master)
2. Jenkins Agent (Slave/Worker)
3. Developer
4. Source Code Repository
5. Build Tools
6. Deployment Environment

---

## Jenkins Architecture Diagram

```text
                 +------------------+
                 |    Developer     |
                 +--------+---------+
                          |
                          |
                          v
                 +------------------+
                 | Git Repository   |
                 | (GitHub/GitLab)  |
                 +--------+---------+
                          |
                          |
                          v
                 +------------------+
                 | Jenkins          |
                 | Controller       |
                 | (Master)         |
                 +--------+---------+
                          |
          +---------------+---------------+
          |                               |
          |                               |
          v                               v

 +------------------+         +------------------+
 | Jenkins Agent 1  |         | Jenkins Agent 2  |
 | Build & Test     |         | Build & Deploy   |
 +--------+---------+         +--------+---------+
          |                            |
          +-------------+--------------+
                        |
                        v
             +------------------+
             | Deployment       |
             | Environment      |
             +------------------+
```

---

# Jenkins Controller (Master)

The Controller is the central server of Jenkins.

### Responsibilities

- Manages Jenkins configuration.
- Schedules jobs.
- Distributes work to agents.
- Maintains build history.
- Monitors build status.
- Manages plugins and users.

### Example

When code is pushed to GitHub:

1. Jenkins Controller receives the trigger.
2. Creates a build job.
3. Assigns the job to an available agent.

---

# Jenkins Agent

Agents are worker machines connected to the Jenkins Controller.

### Responsibilities

- Execute build jobs.
- Run automated tests.
- Deploy applications.
- Report results back to the controller.

### Advantages

- Reduces load on the controller.
- Allows parallel execution.
- Faster build processing.

---

# Jenkins Build Process

### Step 1: Developer Pushes Code

```text
Developer
    ↓
GitHub Repository
```

### Step 2: Jenkins Detects Change

```text
GitHub Webhook
      ↓
 Jenkins Trigger
```

### Step 3: Build Starts

```text
Jenkins
   ↓
Compile Source Code
```

### Step 4: Testing

```text
Run Unit Tests
Run Integration Tests
```

### Step 5: Deployment

```text
Deploy to Server
```

### Step 6: Notification

```text
Email / Slack Notification
```

---

# Jenkins Workflow Diagram

```text
Developer
    ↓
Push Code
    ↓
GitHub Repository
    ↓
Webhook Trigger
    ↓
Jenkins Controller
    ↓
Assign Job
    ↓
Jenkins Agent
    ↓
Build
    ↓
Test
    ↓
Deploy
    ↓
Notification
```

---

# Popular Jenkins Integrations

| Tool | Purpose |
|--------|----------|
| Git | Source Code Management |
| GitHub | Repository Hosting |
| Maven | Build Automation |
| Docker | Containerization |
| Kubernetes | Container Orchestration |
| SonarQube | Code Quality Analysis |
| AWS | Cloud Deployment |
| Slack | Notifications |

---

# Advantages of Jenkins

✅ Free and Open Source

✅ Easy Installation

✅ Large Plugin Ecosystem

✅ Supports CI/CD

✅ Distributed Builds

✅ Automated Testing

✅ Faster Software Delivery

✅ Integrates with Many Tools

---

# Limitations of Jenkins

❌ Initial setup can be complex.

❌ Managing many plugins can be difficult.

❌ Requires maintenance and monitoring.

❌ Controller can become a bottleneck if not properly configured.

---

# Interview Questions

### 1. What is Jenkins?

Jenkins is an open-source automation server used to automate build, test, and deployment processes in CI/CD pipelines.

### 2. What is CI/CD?

CI/CD is the practice of automating software integration, testing, and deployment.

### 3. What are Jenkins Agents?

Agents are worker machines that execute jobs assigned by the Jenkins Controller.

### 4. What is the role of Jenkins Controller?

The Controller manages jobs, agents, plugins, users, and overall Jenkins operations.

### 5. Why is Jenkins widely used?

Because it automates software delivery processes and integrates with many development tools.

---

# Summary

- Jenkins is an open-source CI/CD automation tool.
- It automates Build, Test, and Deployment processes.
- Jenkins follows a Controller-Agent architecture.
- The Controller manages jobs and agents.
- Agents execute build and deployment tasks.
- Jenkins integrates with Git, Docker, Kubernetes, AWS, and many other tools.
- It helps teams deliver software faster and more reliably.
