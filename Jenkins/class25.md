# Class 25 - Jenkins Parameters (Parameterized Jobs)

## What are Parameters in Jenkins?

Parameters allow users to provide input values when starting a Jenkins job. Instead of hardcoding values in the pipeline, parameters make jobs dynamic and reusable.

### Benefits of Parameters
- Run the same pipeline with different inputs.
- Select different branches or environments.
- Control deployment using user input.
- Improve pipeline flexibility.
- Avoid modifying the Jenkinsfile for every execution.

---

# Types of Parameters in Jenkins

Jenkins supports several types of parameters.

## 1. String Parameter

A String parameter accepts a single line of text.

### Example

```groovy
parameters {
    string(
        name: 'VERSION',
        defaultValue: 'v1.0',
        description: 'Enter the application version'
    )
}
```

### User Input

```
VERSION = v2.0
```

### Access in Pipeline

```groovy
echo "${params.VERSION}"
```

Output

```
v2.0
```

---

## 2. Choice Parameter

A Choice parameter displays a dropdown list.

### Example

```groovy
parameters {
    choice(
        name: 'BRANCH',
        choices: ['main', 'dev', 'test'],
        description: 'Select Git branch'
    )
}
```

### Dropdown

```
main
dev
test
```

### Access

```groovy
echo "${params.BRANCH}"
```

Output

```
dev
```

---

## 3. Boolean Parameter

A Boolean parameter displays a checkbox.

### Example

```groovy
parameters {
    booleanParam(
        name: 'DEPLOY_TO_PROD',
        defaultValue: false,
        description: 'Deploy to Production'
    )
}
```

### Access

```groovy
if(params.DEPLOY_TO_PROD){
    echo "Deploying to Production"
}
else{
    echo "Skipping Production Deployment"
}
```

---

## 4. Text Parameter

Used to accept multiple lines of text.

### Example

```groovy
parameters {
    text(
        name: 'MESSAGE',
        defaultValue: 'Deployment Started',
        description: 'Enter deployment message'
    )
}
```

Access

```groovy
echo params.MESSAGE
```

---

## 5. Password Parameter

Stores sensitive values such as passwords or API keys.

### Example

```groovy
parameters {
    password(
        name: 'PASSWORD',
        defaultValue: '',
        description: 'Enter Password'
    )
}
```

Access

```groovy
echo "Password Received"
```

> Never print passwords in Jenkins logs.

---

# Complete Example

```groovy
pipeline {
    agent any

    parameters {

        string(
            name: 'VERSION',
            defaultValue: 'v1.0',
            description: 'Application Version'
        )

        choice(
            name: 'BRANCH',
            choices: ['main','dev','test'],
            description: 'Select Branch'
        )

        booleanParam(
            name: 'DEPLOY_TO_PROD',
            defaultValue: false,
            description: 'Deploy to Production'
        )

        text(
            name: 'DESCRIPTION',
            defaultValue: 'Deployment Started',
            description: 'Deployment Message'
        )
    }

    stages {

        stage('Display Parameters') {
            steps {

                echo "Version : ${params.VERSION}"
                echo "Branch : ${params.BRANCH}"
                echo "Deploy : ${params.DEPLOY_TO_PROD}"
                echo "Description : ${params.DESCRIPTION}"

            }
        }

    }
}
```

---

# Passing Parameters to Jenkins Jobs

## Step 1

Create a Pipeline Job.

```
Dashboard
    ↓
New Item
    ↓
Pipeline
```

---

## Step 2

Configure the Pipeline.

```
Pipeline Script from SCM
```

or

```
Pipeline Script
```

---

## Step 3

Add Parameters in Jenkinsfile.

Example

```groovy
parameters {

    string(
        name: 'VERSION',
        defaultValue: 'v1.0',
        description: 'Application Version'
    )

}
```

---

## Step 4

Save the Pipeline.

After saving, Jenkins displays

```
Build with Parameters
```

instead of

```
Build Now
```

---

## Step 5

Click

```
Build with Parameters
```

You will see

```
VERSION

v1.0
```

Enter your value.

Example

```
v2.5
```

Click

```
Build
```

---

# Using Parameters Inside Pipeline

```groovy
echo "${params.VERSION}"
```

Using in Shell

```groovy
sh """
echo "Version is ${params.VERSION}"
"""
```

---

# Using Parameters for Git Checkout

```groovy
parameters {

    choice(
        name: 'BRANCH',
        choices: ['main','dev']
    )

}
```

Checkout

```groovy
checkout scmGit(
    branches: [[name: "*/${params.BRANCH}"]],
    userRemoteConfigs: [[
        credentialsId: 'git_credentials',
        url: 'https://github.com/example/project.git'
    ]]
)
```

Now the selected branch is checked out dynamically.

---

# Using Boolean Parameter

```groovy
stage('Deploy') {

    when {
        expression {
            params.DEPLOY_TO_PROD
        }
    }

    steps {
        echo "Deploying to Production..."
    }
}
```

If checkbox is

```
Checked
```

Deployment executes.

If checkbox is

```
Unchecked
```

Deployment is skipped.

---

# Real-Time Example

Suppose your company has three environments.

```
Development

Testing

Production
```

Instead of creating three pipelines, use parameters.

```groovy
choice(
    name: 'ENVIRONMENT',
    choices: ['dev','test','prod']
)
```

Then

```groovy
echo "Deploying to ${params.ENVIRONMENT}"
```

This allows one Jenkins pipeline to deploy to multiple environments.

---

# Best Practices

- Use meaningful parameter names.
- Provide default values whenever possible.
- Do not print passwords in the console.
- Use `choice` parameters for predefined options.
- Use `booleanParam` to enable or disable stages.
- Validate user input before deployment.
- Keep parameter names uppercase for readability (optional but commonly followed).

---

# Interview Questions

### 1. What is a parameterized Jenkins job?

A Jenkins job that accepts user input at runtime to make the pipeline dynamic.

---

### 2. What are the common parameter types?

- String
- Choice
- Boolean
- Text
- Password

---

### 3. How do you access parameter values?

```groovy
params.PARAMETER_NAME
```

Example

```groovy
params.VERSION
```

---

### 4. What is the difference between `string` and `choice` parameters?

- **String:** User can enter any value.
- **Choice:** User selects from predefined options.

---

### 5. What is the use of `booleanParam`?

It provides a checkbox to enable or disable a feature, such as production deployment.

---

## Summary

- Parameters make Jenkins pipelines dynamic.
- Jenkins supports String, Choice, Boolean, Text, and Password parameters.
- Parameters are defined inside the `parameters {}` block.
- Access parameter values using `params.PARAMETER_NAME`.
- Parameterized jobs appear as **Build with Parameters** in Jenkins.
- Parameters are commonly used for branch selection, application versioning, environment selection, deployment control, and user input.
