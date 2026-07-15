# Class 22 - Linux Process Management, Git Branching Strategies & Maven

## Topics Covered

1. Running Commands in Background
2. Foreground (fg) Command
3. jobs Command
4. kill Command
5. pkill Command
6. Branching Strategies in Organizations
7. Maven Installation
8. Maven Quickstart Archetype

---

# 1. Running Commands in Background

In Linux, a process can run in the **foreground** or **background**.

- **Foreground Process:** Uses the terminal until it finishes.
- **Background Process:** Runs independently, allowing you to continue using the terminal.

### Running a command in the background

Add **`&`** at the end of the command.

```bash
sleep 300 &
```

Output:

```text
[1] 12345
```

- **1** → Job Number
- **12345** → Process ID (PID)

Example:

```bash
ping google.com &
```

```bash
find / -name "*.log" &
```

---

# 2. View Background Jobs

Use:

```bash
jobs
```

Example:

```bash
jobs
```

Output:

```text
[1]+ Running    sleep 300 &
```

---

# 3. Bring a Background Job to Foreground

Use:

```bash
fg
```

If multiple jobs exist:

```bash
fg %1
```

Example:

```bash
sleep 300 &
jobs
fg %1
```

Now the process runs in the foreground.

---

# 4. Stop a Running Process

Press

```text
Ctrl + C
```

This immediately terminates the running foreground process.

Example:

```bash
ping google.com
```

Press:

```text
Ctrl + C
```

---

# 5. Suspend a Running Process

Press:

```text
Ctrl + Z
```

This pauses the current process.

Example:

```bash
sleep 500
```

Press:

```text
Ctrl + Z
```

Output:

```text
[1]+ Stopped sleep 500
```

Resume it in the background:

```bash
bg
```

Resume it in the foreground:

```bash
fg
```

---

# 6. kill Command

The **kill** command terminates a process using its Process ID (PID).

Find the PID:

```bash
ps -ef
```

or

```bash
ps -ef | grep nginx
```

Terminate:

```bash
kill PID
```

Example:

```bash
kill 12345
```

Force terminate:

```bash
kill -9 12345
```

> **Note:** `kill -9` forcefully stops the process and should be used only when a normal `kill` does not work.

---

# 7. pkill Command

The **pkill** command kills processes by **name** instead of PID.

Syntax:

```bash
pkill process_name
```

Example:

```bash
pkill ping
```

```bash
pkill firefox
```

Kill all Java processes:

```bash
pkill java
```

Verify:

```bash
ps -ef | grep java
```

---

# Difference Between kill and pkill

| kill | pkill |
|------|-------|
| Uses Process ID (PID) | Uses Process Name |
| Need to find PID first | No need to find PID |
| Example: `kill 2345` | Example: `pkill java` |

---

# 8. Branching Strategies in Organizations

Large organizations follow branching strategies to organize development and deployment.

## Why Branching Strategy?

- Multiple developers work together.
- Avoid conflicts.
- Separate development from production.
- Maintain release history.
- Enable parallel feature development.

---

## Common Branches

### main (or master)

- Production-ready code.
- Stable branch.
- Only tested code is merged here.

Example:

```text
main
```

---

### develop

- Integration branch.
- Developers merge completed features here.
- Used for testing before production.

```text
develop
```

---

### feature Branch

Created for developing a specific feature.

Example:

```text
feature/login
feature/payment
feature/profile
```

Flow:

```text
develop
   |
   |---- feature/login
   |
   |---- feature/payment
```

After completion:

```text
feature/login
      ↓
develop
```

---

### release Branch

Created when preparing a production release.

Example:

```text
release/v1.0
```

Purpose:

- Final testing
- Bug fixing
- Version updates

After testing:

```text
release
   ↓
main
```

---

### hotfix Branch

Used to fix urgent production issues.

Example:

```text
hotfix/login-bug
```

Flow:

```text
main
   |
hotfix
   |
main
develop
```

---

# Git Flow Example

```text
                  feature/login
                 /
main -------- develop -------- feature/payment
                \
                 release/v1.0
                      |
                    main
                      |
                  hotfix/v1.0.1
                      |
                    main
```

---

# 9. Maven

## What is Maven?

Apache Maven is a **Build Automation Tool** used primarily for Java projects.

It helps automate:

- Downloading dependencies
- Compiling code
- Running tests
- Packaging applications
- Managing project lifecycle

---

## Advantages

- Dependency Management
- Standard Project Structure
- Build Automation
- Plugin Support
- Easy Integration with Jenkins

---

# Maven Installation (Ubuntu)

## Step 1: Update packages

```bash
sudo apt update
```

---

## Step 2: Install Java

```bash
sudo apt install openjdk-21-jdk -y
```

Verify:

```bash
java -version
```

---

## Step 3: Install Maven

```bash
sudo apt install maven -y
```

Verify:

```bash
mvn -version
```

Example Output:

```text
Apache Maven 3.x.x
Java version: 21
```

---

# 10. Maven Project Structure

```text
MyProject
│
├── src
│   ├── main
│   │   ├── java
│   │   └── resources
│   │
│   └── test
│       └── java
│
├── pom.xml
└── target
```

---

# 11. pom.xml

The **POM (Project Object Model)** file is the heart of every Maven project.

It contains:

- Project information
- Dependencies
- Plugins
- Build configuration
- Java version
- Packaging type

Example:

```xml
<project>
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.company</groupId>

    <artifactId>demo</artifactId>

    <version>1.0</version>
</project>
```

---

# 12. Maven Quickstart Archetype

An **Archetype** is a project template used to generate a standard Maven project structure.

The most commonly used archetype is:

```text
maven-archetype-quickstart
```

Create a new project:

```bash
mvn archetype:generate
```

Choose:

```text
maven-archetype-quickstart
```

Or create directly:

```bash
mvn archetype:generate \
-DgroupId=com.company \
-DartifactId=DemoProject \
-DarchetypeArtifactId=maven-archetype-quickstart \
-DinteractiveMode=false
```

Example:

```bash
mvn archetype:generate \
-DgroupId=com.init \
-DartifactId=StudentApp \
-DarchetypeArtifactId=maven-archetype-quickstart \
-DinteractiveMode=false
```

Generated structure:

```text
StudentApp
│
├── pom.xml
├── src
│   ├── main
│   │   └── java
│   └── test
│       └── java
```

---

# Useful Maven Commands

Check Maven version:

```bash
mvn -version
```

Compile project:

```bash
mvn compile
```

Run tests:

```bash
mvn test
```

Package project:

```bash
mvn package
```

Clean project:

```bash
mvn clean
```

Install project to local repository:

```bash
mvn install
```

Clean and package:

```bash
mvn clean package
```

---

# Summary

- Learned how to run processes in the background using `&`.
- Used `jobs`, `fg`, `Ctrl+C`, and `Ctrl+Z` for process management.
- Understood `kill` and `pkill` commands.
- Explored common Git branching strategies used in organizations (main, develop, feature, release, hotfix).
- Installed Apache Maven and verified the installation.
- Learned about the `pom.xml` file and Maven project structure.
- Created a new Maven project using the `maven-archetype-quickstart` archetype.
```
