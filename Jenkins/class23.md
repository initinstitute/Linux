# Class 23 - Maven Lifecycle and Deploying a WAR File on Apache Tomcat
class URL: https://youtu.be/-sXTK2m9CLE
## Objective

In this class, we covered:

- Understanding the Maven Build Lifecycle
- Common Maven Commands
- Installing Apache Tomcat
- Building a WAR file using Maven
- Deploying the WAR file to Tomcat
- Updating the application by replacing the WAR file

---

# 1. What is Maven?

Apache Maven is a build automation and dependency management tool primarily used for Java projects. It simplifies the process of:

- Compiling source code
- Downloading required dependencies
- Running unit tests
- Packaging applications
- Deploying applications
- Managing project builds

Project configuration is maintained in a single file called **pom.xml** (Project Object Model).

---

# 2. Maven Build Lifecycle

A Maven lifecycle is a sequence of predefined phases that automate the process of building a Java project.

There are three built-in lifecycles:

1. Clean Lifecycle
2. Default (Build) Lifecycle
3. Site Lifecycle

The **Default Lifecycle** is the most commonly used lifecycle.

---

## Maven Default Lifecycle Phases

| Phase | Description |
|--------|-------------|
| validate | Validates the project structure and `pom.xml`. |
| compile | Compiles the Java source code. |
| test | Executes unit tests. |
| package | Packages the application into a JAR or WAR file. |
| verify | Performs additional quality checks. |
| install | Installs the artifact into the local Maven repository (`~/.m2/repository`). |
| deploy | Uploads the artifact to a remote repository such as JFrog or Nexus. |

---

## Maven Lifecycle Flow

```text
validate
    ↓
compile
    ↓
test
    ↓
package
    ↓
verify
    ↓
install
    ↓
deploy
```

---

# 3. Maven Clean Lifecycle

The Clean Lifecycle removes files generated during previous builds.

Common command:

```bash
mvn clean
```

This removes the **target/** directory.

---

# 4. Frequently Used Maven Commands

### Validate Project

```bash
mvn validate
```

Checks whether the project structure is valid.

---

### Compile Source Code

```bash
mvn compile
```

Compiles Java source files.

---

### Run Unit Tests

```bash
mvn test
```

Runs test cases.

---

### Package the Application

```bash
mvn package
```

Creates a JAR or WAR file inside the **target/** directory.

---

### Install into Local Repository

```bash
mvn install
```

Copies the generated artifact into:

```text
~/.m2/repository
```

This allows other local projects to use it.

---

### Deploy to Remote Repository

```bash
mvn deploy
```

Uploads the generated artifact to a remote repository like:

- JFrog Artifactory
- Nexus Repository

---

### Clean and Build

```bash
mvn clean package
```

This command:

1. Deletes old build files
2. Compiles source code
3. Runs tests
4. Creates a new WAR/JAR

---

# 5. Installing Apache Tomcat

Apache Tomcat is a Java application server used to deploy and run Java web applications.

---

## Step 1: Update the Package Repository

```bash
sudo apt update
```

---

## Step 2: Install Java

```bash
sudo apt install openjdk-21-jdk -y
```

Verify installation:

```bash
java -version
```

Example output:

```text
openjdk version "21"
```

---

## Step 3: Download Apache Tomcat

```bash
wget https://downloads.apache.org/tomcat/tomcat-10/v10.1.44/bin/apache-tomcat-10.1.44.tar.gz
```

---

## Step 4: Extract Tomcat

```bash
tar -xvf apache-tomcat-10.1.44.tar.gz
```

---

## Step 5: Rename the Folder (Optional)

```bash
mv apache-tomcat-10.1.44 tomcat
```

---

## Step 6: Navigate to Tomcat

```bash
cd tomcat
```

---

## Step 7: Make Scripts Executable

```bash
chmod +x bin/*.sh
```

---

## Step 8: Start Tomcat

```bash
cd bin

./startup.sh
```

Expected output:

```text
Tomcat started.
```

---

## Step 9: Verify Tomcat Process

```bash
ps -ef | grep tomcat
```

or

```bash
netstat -tulnp | grep 8080
```

---

## Step 10: Access Tomcat

Open your browser:

```text
http://<EC2-Public-IP>:8080
```

Example:

```text
http://13.233.150.25:8080
```

Tomcat Home Page should appear.

---

# 6. Build the WAR File

Navigate to your Maven project.

Example:

```bash
cd calculator_maven_demo
```

Build the project:

```bash
mvn clean package
```

After a successful build:

```text
target/
```

contains:

```text
calculator.war
```

or

```text
calculator-1.0.war
```

---

# 7. Deploy WAR File to Tomcat

Navigate to the Tomcat webapps directory:

```bash
cd ~/tomcat/webapps
```

Copy the WAR file:

```bash
cp ~/calculator_maven_demo/target/calculator.war .
```

Tomcat automatically extracts the WAR file.

Example:

```text
calculator.war

calculator/
```

---

# 8. Run the Application

Open the application:

```text
http://<EC2-Public-IP>:8080/calculator
```

Example:

```text
http://13.233.150.25:8080/calculator
```

---

# 9. Updating the Application

Whenever code changes are made:

### Step 1

Modify the source code.

---

### Step 2

Generate a new WAR file.

```bash
mvn clean package
```

---

### Step 3

Stop Tomcat.

```bash
cd ~/tomcat/bin

./shutdown.sh
```

---

### Step 4

Remove the old application.

```bash
rm -rf ~/tomcat/webapps/calculator

rm ~/tomcat/webapps/calculator.war
```

---

### Step 5

Copy the new WAR file.

```bash
cp target/calculator.war ~/tomcat/webapps/
```

---

### Step 6

Start Tomcat.

```bash
cd ~/tomcat/bin

./startup.sh
```

Tomcat automatically deploys the latest WAR.

---

# 10. Complete Deployment Flow

```text
Developer
      │
      ▼
Modify Source Code
      │
      ▼
mvn clean package
      │
      ▼
Generated WAR File
      │
      ▼
Stop Tomcat
      │
      ▼
Delete Old WAR
      │
      ▼
Copy New WAR
      │
      ▼
Start Tomcat
      │
      ▼
Tomcat Extracts WAR
      │
      ▼
Application Available on Port 8080
```

---

# 11. Useful Tomcat Commands

### Start Tomcat

```bash
cd ~/tomcat/bin

./startup.sh
```

---

### Stop Tomcat

```bash
./shutdown.sh
```

---

### Restart Tomcat

```bash
./shutdown.sh

./startup.sh
```

---

### Check Running Process

```bash
ps -ef | grep tomcat
```

---

### Check Port

```bash
netstat -tulnp | grep 8080
```

---

### View Logs

```bash
tail -f ~/tomcat/logs/catalina.out
```

---

# 12. Directory Structure

```text
calculator_maven_demo/
│
├── pom.xml
├── src/
│   ├── main/
│   └── test/
│
└── target/
    └── calculator.war


tomcat/
│
├── bin/
├── conf/
├── logs/
├── temp/
├── webapps/
│     ├── ROOT
│     ├── calculator.war
│     └── calculator/
└── work/
```

---

# 13. Key Interview Questions

### What is Maven?

Maven is a build automation and dependency management tool for Java projects.

---

### What is the purpose of `pom.xml`?

It contains project information, dependencies, plugins, repositories, and build configuration.

---

### What is the difference between `package` and `install`?

- `package` creates the JAR/WAR file.
- `install` creates the artifact and stores it in the local Maven repository.

---

### What does `deploy` do?

It uploads the generated artifact to a remote repository such as JFrog or Nexus.

---

### What is a WAR file?

A WAR (Web Application Archive) is a packaged Java web application that can be deployed to web servers like Apache Tomcat.

---

### Where should a WAR file be copied?

```text
tomcat/webapps/
```

---

### What happens after copying the WAR file?

Tomcat automatically extracts the WAR file and deploys the application.

---

### How do you deploy a new version of the application?

1. Modify the code.
2. Run `mvn clean package`.
3. Stop Tomcat.
4. Remove the old WAR and extracted folder.
5. Copy the new WAR file to `webapps`.
6. Start Tomcat.
7. Access the updated application in the browser.

---

# Summary

- Maven automates Java project builds using predefined lifecycle phases.
- The default lifecycle includes **validate → compile → test → package → verify → install → deploy**.
- Apache Tomcat is a web server and servlet container used to run Java web applications.
- Maven generates a WAR file using `mvn clean package`.
- The WAR file is deployed by copying it into Tomcat's `webapps` directory.
- Replacing the old WAR file with a newly built WAR and restarting Tomcat deploys the latest version of the application.
