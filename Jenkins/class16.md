
# Jenkins - Configure Worker (Slave) Node and Pipelines

## Objective

* Install Java JDK on the worker node.
* Create Jenkins workspace.
* Connect the worker (slave) node with the Jenkins master.
* Understand Declarative Pipeline and Scripted Pipeline.
* Create sample Jenkins pipelines.

---

# 1. Prerequisites

### Master Node

* Jenkins installed
* Java JDK installed
* Jenkins running

### Worker Node

* Ubuntu/Linux machine
* Internet connection
* SSH access from Master to Worker
* Java JDK installed

---

# 2. Install Java JDK on Worker Node

Update packages

```bash
sudo apt update
```

Install OpenJDK 17

```bash
sudo apt install openjdk-17-jdk -y
```

Verify installation

```bash
java -version
```

Expected Output

```
openjdk version "17.x.x"
```

Check JAVA_HOME

```bash
readlink -f $(which java)
```

---

# 3. Create Jenkins Workspace

Create directory

```bash
sudo mkdir -p /home/ubuntu/jenkins
```

Give permissions

```bash
sudo chown -R ubuntu:ubuntu /home/ubuntu/jenkins
```

---

# 4. Configure SSH Access

On Jenkins Master

Generate SSH Key

```bash
ssh-keygen
```

Copy key to Worker

```bash
ssh-copy-id ubuntu@<worker-ip>
```

Test SSH

```bash
ssh ubuntu@<worker-ip>
```

If login works without password, SSH configuration is successful.

---

# 5. Add Worker Node in Jenkins

Open Jenkins

```
Manage Jenkins
        ↓
Nodes
        ↓
New Node
```

Enter

```
Node Name:
worker-node
```

Select

```
Permanent Agent
```

Click OK.

---

# 6. Configure Worker Node

Example configuration

```
Name:
worker-node

Remote Root Directory:
/home/ubuntu/jenkins

Labels:
worker

Usage:
Use this node as much as possible

Launch Method:
Launch agents via SSH

Host:
<worker-ip>

Credentials:
SSH Username and Private Key

Host Key Verification:
Non verifying Verification Strategy
```

Click Save.

---

# 7. Verify Connection

After saving

```
Node Status

Connected ✔

Online ✔
```

Worker node is now connected to the Jenkins master.

---

# 8. Jenkins Pipeline

A Jenkins Pipeline is a script that automates the software build, test, and deployment process.

Pipelines are written in **Groovy**.

There are two types:

1. Declarative Pipeline
2. Scripted Pipeline

---

# 9. Declarative Pipeline

Declarative Pipeline uses a simple and structured syntax.

### Features

* Easy to read
* Beginner friendly
* Fixed structure
* Recommended by Jenkins

### Syntax

```groovy
pipeline {

    agent any

    stages {

        stage('Build') {

            steps {
                echo 'Building Application'
            }

        }

        stage('Test') {

            steps {
                echo 'Running Tests'
            }

        }

        stage('Deploy') {

            steps {
                echo 'Deploying Application'
            }

        }

    }

}
```

### Explanation

```
pipeline
```

Defines the pipeline.

```
agent any
```

Run on any available Jenkins node.

```
stages
```

Contains all stages.

```
stage
```

Represents one phase of the pipeline.

```
steps
```

Contains commands executed in the stage.

```
echo
```

Prints messages in Jenkins console.

---

# 10. Declarative Pipeline on Worker Node

```groovy
pipeline {

    agent {
        label 'worker'
    }

    stages {

        stage('Workspace') {

            steps {
                sh 'pwd'
                sh 'hostname'
            }

        }

        stage('Build') {

            steps {
                sh 'java -version'
            }

        }

    }

}
```

This pipeline runs only on the worker node labeled **worker**.

---

# 11. Scripted Pipeline

Scripted Pipeline provides full programming capabilities using Groovy.

### Features

* Flexible
* Advanced
* Supports loops and conditions
* Suitable for complex CI/CD workflows

### Example

```groovy
node {

    stage('Build') {
        echo "Building..."
    }

    stage('Test') {
        echo "Testing..."
    }

    stage('Deploy') {
        echo "Deploying..."
    }

}
```

---

# 12. Scripted Pipeline with Worker Node

```groovy
node('worker') {

    stage('Java Version') {
        sh 'java -version'
    }

    stage('Workspace') {
        sh 'pwd'
    }

    stage('Build') {
        echo "Build Successful"
    }

}
```

The pipeline executes only on the Jenkins worker node labeled **worker**.

---

# 13. Declarative vs Scripted Pipeline

| Feature        | Declarative Pipeline | Scripted Pipeline  |
| -------------- | -------------------- | ------------------ |
| Syntax         | Simple               | Flexible           |
| Language       | Groovy DSL           | Pure Groovy        |
| Readability    | Easy                 | Moderate           |
| Learning       | Beginner-friendly    | Advanced           |
| Best For       | Standard CI/CD       | Complex workflows  |
| Error Handling | Built-in             | Manual             |
| Recommended    | Yes                  | For advanced users |

---

# 14. Advantages of Worker Nodes

* Distributes workload.
* Executes multiple jobs simultaneously.
* Improves build performance.
* Supports different operating systems.
* Isolates build environments.
* Scales Jenkins infrastructure.

---

# Interview Questions

### What is a Jenkins Worker Node?

A worker (agent/slave) node is a machine that executes Jenkins jobs assigned by the master (controller).

---

### Why is Java required on the Worker Node?

Because Jenkins agents run as Java applications.

---

### What is a Workspace?

A workspace is the directory where Jenkins stores source code, build files, and artifacts during job execution.

---

### Difference between Declarative and Scripted Pipeline?

Declarative Pipeline:

* Simple syntax
* Easy to maintain
* Recommended for most projects

Scripted Pipeline:

* Flexible
* Supports advanced Groovy programming
* Best for complex CI/CD logic

---

# Summary

* Installed Java JDK on the worker node.
* Created a Jenkins workspace.
* Connected the worker node to the Jenkins master via SSH.
* Learned the difference between Declarative and Scripted Pipelines.
* Created sample pipelines to execute builds on both any agent and a specific worker node.
