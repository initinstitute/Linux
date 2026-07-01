# Class 17 Notes: Scripted vs Declarative Jenkins Pipeline
LINK: https://youtu.be/2PgwprTVi0k
## What is a Jenkins Pipeline?

A Jenkins Pipeline is a collection of steps that automates the software build, test, and deployment process. Pipelines are written using **Groovy**.

Jenkins supports two types of pipelines:

1. Scripted Pipeline
2. Declarative Pipeline

---

# 1. Scripted Pipeline

A Scripted Pipeline provides complete control over the pipeline logic using Groovy code.

### Features

* More flexible
* Suitable for complex workflows
* Written entirely in Groovy
* Requires more scripting knowledge

### Syntax

```groovy
node {
    stage('Build') {
        echo 'Building application...'
    }

    stage('Wait') {
        sleep(time: 100, unit: 'SECONDS')
    }

    stage('Test') {
        echo 'Running tests...'
    }
}
```

### Characteristics

* Uses `node {}` as the entry point.
* Stages are created manually.
* Easier to implement custom logic.
* Best for advanced users.

---

# 2. Declarative Pipeline

A Declarative Pipeline provides a structured and easier-to-read syntax.

### Features

* Simple and readable
* Recommended by Jenkins
* Easy to maintain
* Built-in validation

### Syntax

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building application...'
            }
        }

        stage('Wait') {
            steps {
                sleep(time: 100, unit: 'SECONDS')
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }
    }
}
```

### Characteristics

* Uses `pipeline {}` as the entry point.
* Organizes stages inside a `stages` block.
* Commands are executed inside `steps`.
* Easier for beginners and teams.

---

# Scripted vs Declarative Pipeline

| Feature         | Scripted Pipeline | Declarative Pipeline     |
| --------------- | ----------------- | ------------------------ |
| Entry Point     | `node {}`         | `pipeline {}`            |
| Language        | Groovy            | Structured Groovy syntax |
| Flexibility     | High              | Moderate                 |
| Readability     | Moderate          | High                     |
| Learning Curve  | Higher            | Lower                    |
| Validation      | Limited           | Built-in validation      |
| Recommended For | Complex workflows | Most Jenkins projects    |

---

# Sleep Command

Pause the pipeline for 100 seconds.

### Scripted Pipeline

```groovy
node {
    stage('Wait') {
        sleep(time: 100, unit: 'SECONDS')
    }
}
```

### Declarative Pipeline

```groovy
pipeline {
    agent any

    stages {
        stage('Wait') {
            steps {
                sleep(time: 100, unit: 'SECONDS')
            }
        }
    }
}
```

---

# Common Jenkins Pipeline Steps

* `echo` – Print a message.
* `sh` – Execute a shell command (Linux/macOS agents).
* `bat` – Execute a Windows command.
* `sleep` – Pause pipeline execution.
* `checkout` – Download source code from version control.
* `archiveArtifacts` – Store build artifacts.
* `junit` – Publish test results.

---

# Best Practices

* Prefer Declarative Pipelines for most projects.
* Use meaningful stage names.
* Store pipeline code in a `Jenkinsfile`.
* Keep pipeline logic simple and reusable.
* Use Scripted Pipelines only when advanced customization is required.

---

# Summary

* **Scripted Pipeline** offers maximum flexibility and is best for advanced use cases.
* **Declarative Pipeline** is simpler, easier to read, and the recommended choice for most Jenkins projects.
* Use the `sleep(time: 100, unit: 'SECONDS')` step to pause a pipeline for 100 seconds.
