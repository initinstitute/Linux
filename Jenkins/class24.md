# Class 25 - Jenkins Maven Integration and Tomcat Deployment

## Objective

In this class, we learned:

- Integrating Maven with Jenkins
- Configuring Maven as a Global Tool
- Creating a Jenkins Pipeline using Jenkinsfile
- Checking out source code from GitHub
- Building a Maven project using `mvn clean install`
- Deploying the generated WAR file to Apache Tomcat
- Best practices for deploying applications through Jenkins

---

# Architecture

```text
GitHub Repository
        │
        ▼
Jenkins Pipeline
        │
        ▼
Checkout Source Code
        │
        ▼
Maven Build
(mvn clean install)
        │
        ▼
Generate WAR File
(target/*.war)
        │
        ▼
Deploy WAR to Tomcat
        │
        ▼
Access Application
http://<server-ip>:8080
```

---

# Step 1: Install Maven Plugin in Jenkins

Navigate to

```
Manage Jenkins
        ↓
Plugins
        ↓
Available Plugins
```

Search for

```
Maven Integration
```

Install the plugin.

---

# Step 2: Configure Maven Tool

Navigate to

```
Manage Jenkins
        ↓
Tools
        ↓
Maven Installations
```

Add a Maven installation.

Example

```
Name

maven-3.9.16
```

Choose either

- Install automatically
- Or specify the Maven home directory

Save the configuration.

---

# Step 3: Create Pipeline Job

Create

```
New Item
```

Choose

```
Pipeline
```

Configure

- Git Repository
- Jenkinsfile
- Credentials

Save.

---

# Jenkins Pipeline Stages

A good pipeline generally contains the following stages.

```
Checkout
        ↓
Build
        ↓
Test
        ↓
Package
        ↓
Deploy
        ↓
Notification
```

---

# Stage 1 — Checkout Source Code

This stage downloads the source code from GitHub.

```groovy
stage('Checkout') {
    steps {
        checkout scmGit(
            branches: [[name: '*/main']],
            userRemoteConfigs: [[
                credentialsId: 'git_cred_maven',
                url: 'https://github.com/initinstitute/calculator_maven_demo.git'
            ]]
        )
    }
}
```

---

# Stage 2 — Build Maven Project

Compile the application and generate the WAR file.

```groovy
stage('Build') {
    steps {
        sh 'mvn clean install'
    }
}
```

Generated artifact

```
target/
    calculator-maven-demo.war
```

---

# Stage 3 — Deploy WAR File

The deployment stage copies the generated WAR file to Tomcat.

### Stop Tomcat (Optional)

```bash
sudo systemctl stop tomcat10
```

### Remove Existing ROOT Application

```bash
sudo rm -rf /var/lib/tomcat10/webapps/ROOT
sudo rm -f /var/lib/tomcat10/webapps/ROOT.war
```

### Copy New WAR

```bash
sudo cp target/*.war /var/lib/tomcat10/webapps/ROOT.war
```

### Start Tomcat

```bash
sudo systemctl start tomcat10
```

### Verify

```bash
sudo systemctl status tomcat10
```

---

# Tomcat Deployment Flow

```text
Build WAR
      │
      ▼
Remove old ROOT application
      │
      ▼
Copy new ROOT.war
      │
      ▼
Restart Tomcat
      │
      ▼
Tomcat extracts ROOT.war
      │
      ▼
Application Available
```

---

# Access the Application

```
http://<EC2-Public-IP>:8080
```

Example

```
http://13.xxx.xxx.xxx:8080
```

---

# Recommended Jenkins Pipeline

```groovy
pipeline {
    agent any

    tools {
        maven 'maven-3.9.16'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        credentialsId: 'git_cred_maven',
                        url: 'https://github.com/initinstitute/calculator_maven_demo.git'
                    ]]
                )
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Deploy to Tomcat') {
    steps {
        sh '''
        echo "========== Deploying WAR to Remote Tomcat Server =========="

        # Variables
        TOMCAT_USER=ubuntu
        TOMCAT_HOST=<TOMCAT_PUBLIC_IP_OR_DNS>
        PEM_KEY=/var/lib/jenkins/init1.pem

        # Copy WAR file from Jenkins Worker to Tomcat Server
        scp -o StrictHostKeyChecking=no \
            -i ${PEM_KEY} \
            target/*.war \
            ${TOMCAT_USER}@${TOMCAT_HOST}:/home/ubuntu/

        # Login to Tomcat Server and deploy the application
        ssh -o StrictHostKeyChecking=no \
            -i ${PEM_KEY} \
            ${TOMCAT_USER}@${TOMCAT_HOST} << 'EOF'

        sudo systemctl stop tomcat10

        sudo rm -rf /var/lib/tomcat10/webapps/ROOT
        sudo rm -f /var/lib/tomcat10/webapps/ROOT.war

        sudo mv /home/ubuntu/*.war /var/lib/tomcat10/webapps/ROOT.war

        sudo systemctl start tomcat10

        sudo systemctl status tomcat10 --no-pager

        EOF
        '''
    }
}
        stage('Notification') {
            steps {
                echo 'Application deployed successfully.'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed.'
        }

        always {
            cleanWs()
        }
    }
}
```

---

# Why Your Original Pipeline Needed Correction

### Duplicate Stage Names

You had two stages named:

```groovy
stage('Notification')
```

Each stage name in a pipeline must be unique.

---

### Parallel Block Misuse

Deployment should occur **after** the build is complete. Running the build and deployment in parallel can cause deployment to start before the WAR file is generated.

**Incorrect:**

```text
Checkout
        │
        ▼
Parallel
   ├── Build
   ├── Deploy
   └── Notification
```

**Correct:**

```text
Checkout
        │
        ▼
Build
        │
        ▼
Deploy
        │
        ▼
Notification
```

---

### Installing Tomcat During Every Build

Avoid installing Tomcat in every pipeline run.

Incorrect:

```bash
sudo apt install tomcat10
```

Install Tomcat **once** during server setup. The pipeline should only deploy the application.

---

### Copying WAR File

Instead of

```bash
cp target/....war
```

use

```bash
sudo cp target/*.war /var/lib/tomcat10/webapps/ROOT.war
```

or specify the exact WAR filename if known.

---

### Restarting Tomcat

After deploying a new WAR file, restart the Tomcat service to ensure the updated application is loaded.

```bash
sudo systemctl restart tomcat10
```

---

# Best Practices

- Use Jenkins credentials for Git authentication.
- Configure Maven in **Manage Jenkins → Tools**.
- Keep Tomcat installed and running; do not install it in each build.
- Build before deployment.
- Use separate pipeline stages with meaningful names.
- Use `post` actions for notifications and workspace cleanup.
- Verify Tomcat status after deployment.
- Monitor Jenkins console output for troubleshooting.

---

# Summary

By the end of this class, you should be able to:

- Configure Maven in Jenkins.
- Create a Jenkins Pipeline using a Jenkinsfile.
- Clone a Maven project from GitHub.
- Build the project using `mvn clean install`.
- Generate a WAR artifact.
- Deploy the WAR file to an Apache Tomcat server.
- Structure Jenkins pipelines following CI/CD best practices.
