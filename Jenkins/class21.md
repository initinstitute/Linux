# 📘 Class 21 Notes – Jenkins Environment Variables, Parallel Block & Git Checkout
URL: https://youtu.be/4vLaq_VhjJA

## 1. Jenkins Environment Variables

### What are Environment Variables?

Environment variables are key-value pairs that store configuration values used during a Jenkins build. They help avoid hardcoding values inside pipelines.

### Why Use Environment Variables?

- Store reusable values.
- Improve pipeline readability.
- Easily change configuration without modifying multiple lines.
- Store credentials and URLs securely (using Jenkins Credentials).

---

## Types of Environment Variables

### 1. Global Environment Variables

Configured in:

```
Manage Jenkins → System → Global Properties → Environment Variables
```

These variables are available to all Jenkins jobs.

Example:

```text
APP_NAME=DevOpsApp
ENVIRONMENT=Production
```

---

### 2. Pipeline Environment Variables

Defined inside the `environment` block.

Example:

```groovy
pipeline {
    agent any

    environment {
        APP_NAME = "DevOpsApp"
        VERSION = "1.0"
    }

    stages {
        stage('Print Variables') {
            steps {
                sh 'echo $APP_NAME'
                sh 'echo $VERSION'
            }
        }
    }
}
```

Output:

```
DevOpsApp
1.0
```

---

### 3. Environment Variables Inside a Stage

Variables can also be declared only for a specific stage.

Example:

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            environment {
                BUILD_ENV = "Development"
            }

            steps {
                sh 'echo $BUILD_ENV'
            }
        }
    }
}
```

---

## Passing Environment Variables Between Stages

Environment variables declared in the pipeline are accessible in all stages.

Example:

```groovy
pipeline {
    agent any

    environment {
        APP = "Jenkins"
    }

    stages {

        stage('Stage 1') {
            steps {
                sh 'echo $APP'
            }
        }

        stage('Stage 2') {
            steps {
                sh 'echo $APP'
            }
        }
    }
}
```

Output:

```
Jenkins
Jenkins
```

---

## Common Built-in Jenkins Environment Variables

| Variable | Description |
|----------|-------------|
| `BUILD_NUMBER` | Current build number |
| `BUILD_ID` | Unique build ID |
| `JOB_NAME` | Jenkins job name |
| `BUILD_URL` | URL of the build |
| `WORKSPACE` | Workspace directory |
| `NODE_NAME` | Agent executing the build |
| `JENKINS_HOME` | Jenkins home directory |
| `GIT_BRANCH` | Current Git branch |
| `GIT_COMMIT` | Current commit ID |

Example:

```groovy
steps {
    sh 'echo $BUILD_NUMBER'
    sh 'echo $JOB_NAME'
    sh 'echo $WORKSPACE'
}
```

---

# 2. Parallel Block

## What is a Parallel Block?

The `parallel` block allows multiple stages to execute at the same time instead of one after another.

This reduces the total pipeline execution time.

---

## Sequential Execution

```
Build
   ↓
Test
   ↓
Deploy
```

Each stage waits for the previous one to complete.

---

## Parallel Execution

```
           Build
             |
    -------------------
    |                 |
Unit Test       Integration Test
    |                 |
    -------------------
             |
          Deploy
```

The testing stages run simultaneously.

---

## Example Pipeline

```groovy
pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo "Building Application..."
            }
        }

        stage('Testing') {

            parallel {

                stage('Unit Test') {
                    steps {
                        echo "Running Unit Tests..."
                    }
                }

                stage('Integration Test') {
                    steps {
                        echo "Running Integration Tests..."
                    }
                }

                stage('Security Test') {
                    steps {
                        echo "Running Security Scan..."
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying Application..."
            }
        }
    }
}
```

---

## Execution Flow

```
Build
   |
------------------------------------
|            |                     |
Unit Test  Integration Test  Security Test
------------------------------------
             |
          Deploy
```

---

## Advantages of Parallel Block

- Faster pipeline execution.
- Better resource utilization.
- Independent tasks run simultaneously.
- Commonly used for testing and scanning.

---

## Common Use Cases

- Unit Testing
- Integration Testing
- UI Testing
- Security Scanning
- Multiple Application Builds
- Multi-platform Testing

---

# 3. Git Checkout Block

## What is Git Checkout?

The checkout block is used to clone or fetch source code from a Git repository into the Jenkins workspace.

It is usually the first step in a CI/CD pipeline.

---

## Basic Checkout

```groovy
checkout scm
```

This checks out the repository configured in the Jenkins job.

---

## Checkout Using Git URL

```groovy
checkout([
    $class: 'GitSCM',
    branches: [[name: '*/main']],
    userRemoteConfigs: [[
        url: 'https://github.com/example/demo.git'
    ]]
])
```

---

## Checkout with Credentials

```groovy
checkout([
    $class: 'GitSCM',
    branches: [[name: '*/main']],
    userRemoteConfigs: [[
        url: 'https://github.com/example/demo.git',
        credentialsId: 'github-credentials'
    ]]
])
```

---

## Declarative Pipeline Example

```groovy
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-credentials',
                    url: 'https://github.com/example/demo.git'
            }
        }

        stage('Build') {
            steps {
                sh 'ls -la'
            }
        }
    }
}
```

---

## Checking Out a Different Branch

```groovy
git branch: 'develop',
    credentialsId: 'github-credentials',
    url: 'https://github.com/example/demo.git'
```

---

## Why Use Git Checkout?

- Downloads the latest source code.
- Fetches the selected branch.
- Retrieves project files into the Jenkins workspace.
- Enables build, test, and deployment stages.

---

# Complete Pipeline Example

```groovy
pipeline {

    agent any

    environment {
        APP_NAME = "DemoApp"
        VERSION = "1.0"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-credentials',
                    url: 'https://github.com/example/demo.git'
            }
        }

        stage('Build') {
            steps {
                echo "Building ${APP_NAME}"
            }
        }

        stage('Testing') {

            parallel {

                stage('Unit Test') {
                    steps {
                        echo "Running Unit Tests"
                    }
                }

                stage('Integration Test') {
                    steps {
                        echo "Running Integration Tests"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying Version ${VERSION}"
            }
        }
    }
}
```

---

# Summary

- **Environment Variables** are used to store reusable configuration values in Jenkins pipelines.
- **Pipeline and Stage Environment Blocks** allow variables to be shared across stages or limited to a single stage.
- **Parallel Block** executes multiple stages simultaneously, reducing overall build time.
- **Git Checkout Block** retrieves source code from a Git repository into the Jenkins workspace before build and deployment.
- Combining **Environment Variables**, **Parallel Execution**, and **Git Checkout** results in efficient, maintainable, and scalable Jenkins pipelines.
