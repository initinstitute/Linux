# class24.md

# Jenkins Declarative Pipeline - Error Handling and Post Actions

Error handling is an essential part of Jenkins pipelines. It helps handle failures gracefully, continue pipeline execution when required, perform cleanup, and send notifications after pipeline execution.

---

# 1. try-catch Block

The `try-catch` block is a Groovy feature used inside the `script` block of a Declarative Pipeline.

## Purpose

- Handle exceptions without stopping the entire pipeline.
- Execute custom logic when an error occurs.
- Log meaningful error messages.
- Continue execution after handling exceptions (if desired).

## Syntax

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    try {
                        echo "Starting Build..."
                        sh "invalid_command"
                    } catch (Exception e) {
                        echo "Error: ${e}"
                    }
                }
            }
        }
    }
}
```

## Output

```
Starting Build...
invalid_command: command not found
Error: hudson.AbortException: script returned exit code 127
```

## Advantages

- Gives full control over exception handling.
- Allows custom error messages.
- Can execute additional logic inside the `catch` block.
- Useful for complex scripting.

---

# 2. catchError Step

`catchError` is a Jenkins Pipeline step used to catch errors without failing the entire pipeline.

## Purpose

- Continue pipeline execution after a stage failure.
- Set build status explicitly.
- Set stage status independently.

## Syntax

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                    sh "invalid_command"
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "Deployment Stage Executed"
            }
        }
    }
}
```

Even if the Build stage fails, the Deploy stage will execute.

## Parameters

| Parameter | Description |
|-----------|-------------|
| buildResult | Result of the entire build |
| stageResult | Result of the current stage |

Possible values:

- SUCCESS
- FAILURE
- UNSTABLE
- ABORTED

## Example

```groovy
catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
    sh "exit 1"
}
```

Result:

- Stage → FAILURE
- Build → UNSTABLE
- Pipeline continues.

---

# Difference Between try-catch and catchError

| try-catch | catchError |
|------------|-----------|
| Groovy feature | Jenkins Pipeline step |
| Used inside `script {}` | Used directly inside `steps {}` |
| Manual exception handling | Automatic build status handling |
| Full control over exceptions | Easier pipeline error handling |
| Can execute custom logic | Mainly controls stage/build result |

---

# 3. when Block

The `when` block controls whether a stage should execute based on specified conditions.

## Purpose

- Execute stages conditionally.
- Skip unnecessary stages.
- Deploy only from specific branches.
- Execute production deployments safely.

## Syntax

```groovy
pipeline {
    agent any

    stages {

        stage('Deploy') {

            when {
                branch 'main'
            }

            steps {
                echo "Deploying Application"
            }
        }
    }
}
```

The Deploy stage executes only when the branch is `main`.

---

## when using Environment Variable

```groovy
when {
    environment name: 'ENV', value: 'PROD'
}
```

---

## when using Expression

```groovy
when {
    expression {
        return env.BUILD_NUMBER.toInteger() > 5
    }
}
```

---

## when using Branch

```groovy
when {
    branch 'develop'
}
```

---

## when using anyOf

```groovy
when {
    anyOf {
        branch 'main'
        branch 'release'
    }
}
```

Runs if either condition is true.

---

## when using allOf

```groovy
when {
    allOf {
        branch 'main'
        environment name: 'DEPLOY', value: 'true'
    }
}
```

Runs only when all conditions are true.

---

## when using not

```groovy
when {
    not {
        branch 'develop'
    }
}
```

Runs for every branch except `develop`.

---

## Common when Conditions

| Condition | Description |
|-----------|-------------|
| branch | Execute for specific branch |
| environment | Check environment variable |
| expression | Custom Groovy condition |
| anyOf | Any one condition must be true |
| allOf | All conditions must be true |
| not | Reverse a condition |
| tag | Execute for Git tags |
| buildingTag() | Execute only for tag builds |
| changeRequest() | Execute for Pull Requests |

---

# 4. post Block

The `post` block executes after a stage or the entire pipeline completes.

It is commonly used for:

- Sending notifications
- Cleaning workspace
- Uploading reports
- Archiving artifacts
- Sending emails
- Printing build status

---

# Pipeline-Level post Block

```groovy
pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo "Building Application..."
            }
        }
    }

    post {

        always {
            echo "Pipeline Finished"
        }

        success {
            echo "Build Successful"
        }

        failure {
            echo "Build Failed"
        }

        unstable {
            echo "Build Unstable"
        }

        aborted {
            echo "Build Aborted"
        }

        changed {
            echo "Build Status Changed"
        }

        cleanup {
            cleanWs()
        }
    }
}
```

---

# Stage-Level post Block

```groovy
stage('Test') {

    steps {
        sh "mvn test"
    }

    post {

        success {
            echo "Tests Passed"
        }

        failure {
            echo "Tests Failed"
        }
    }
}
```

---

# post Conditions

| Condition | Description |
|-----------|-------------|
| always | Runs every time |
| success | Runs after successful build |
| failure | Runs after failed build |
| unstable | Runs when build becomes unstable |
| aborted | Runs when build is aborted |
| changed | Runs when build result changes from previous build |
| cleanup | Runs at the end for cleanup |

---

# Complete Example

```groovy
pipeline {

    agent any

    stages {

        stage('Build') {

            when {
                branch 'main'
            }

            steps {

                catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {

                    script {

                        try {
                            sh "mvn clean package"
                        }

                        catch(Exception e) {
                            echo "Exception: ${e}"
                        }

                    }

                }

            }

            post {

                success {
                    echo "Build Completed Successfully"
                }

                failure {
                    echo "Build Failed"
                }

            }
        }

        stage('Deploy') {

            when {
                environment name: 'DEPLOY', value: 'true'
            }

            steps {
                echo "Deploying Application..."
            }

        }

    }

    post {

        always {
            echo "Pipeline Finished"
        }

        success {
            echo "Pipeline Successful"
        }

        failure {
            echo "Pipeline Failed"
        }

        cleanup {
            cleanWs()
        }

    }

}
```

---

# Best Practices

- Use `try-catch` for handling Groovy exceptions and implementing custom logic.
- Use `catchError` to continue the pipeline after a stage failure.
- Use `when` blocks to execute stages only when necessary.
- Use `post` blocks for cleanup, notifications, and artifact archiving.
- Protect production deployments using `when` conditions.
- Use `cleanWs()` inside `cleanup` to keep Jenkins workspaces clean.
- Keep pipeline stages independent whenever possible.

---

# Interview Questions

## 1. What is the difference between `try-catch` and `catchError`?

**Answer:**

- `try-catch` is a Groovy feature used inside `script {}` to manually handle exceptions.
- `catchError` is a Jenkins Pipeline step that catches errors, sets the build/stage status, and allows the pipeline to continue.

---

## 2. What is the purpose of the `when` block?

**Answer:**

The `when` block is used to execute a stage only when specified conditions are satisfied, such as branch name, environment variable, tag, or custom expression.

---

## 3. What is the purpose of the `post` block?

**Answer:**

The `post` block executes actions after a stage or pipeline completes. It is commonly used for notifications, cleanup, artifact archiving, and reporting.

---

## 4. Can a Declarative Pipeline have multiple `post` blocks?

**Answer:**

Yes. A Declarative Pipeline can have:

- One pipeline-level `post` block.
- Multiple stage-level `post` blocks (one inside each stage).

---

## 5. Which is preferred for Jenkins pipeline failures: `try-catch` or `catchError`?

**Answer:**

- Use `catchError` for normal pipeline error handling and controlling build results.
- Use `try-catch` when custom exception handling or additional Groovy logic is required.
