# 📘 Class 14 Notes - Installation of Jenkins Application

## 🚀 What is Jenkins?

:contentReference[oaicite:0]{index=0} is an open-source automation server used for:

- Continuous Integration (CI)
- Continuous Deployment (CD)
- Automating build and test processes
- Deploying applications

---

## 🖥️ Prerequisites for Jenkins Installation

Before installing Jenkins, make sure your system has:

- Ubuntu / Linux system
- Java installed (JDK 11 or 17 recommended)
- Internet connection
- sudo privileges

---

## ☕ Step 1: Install Java (JDK)

Jenkins requires Java to run.

```bash
sudo apt update
sudo apt install openjdk-17-jdk -y
```

### Verify Java Installation

```bash
java -version
```

---

## 📦 Step 2: Add Jenkins Repository Key

```bash
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```

---

## 📂 Step 3: Add Jenkins Repository

```bash
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
```

---

## 🔄 Step 4: Update System Packages

```bash
sudo apt update
```

---

## ⚙️ Step 5: Install Jenkins

```bash
sudo apt install jenkins -y
```

---

## ▶️ Step 6: Start Jenkins Service

```bash
sudo systemctl start jenkins
```

---

## 🔍 Step 7: Check Jenkins Status

```bash
sudo systemctl status jenkins
```

If running successfully, you will see **active (running)**.

---

## 🌐 Step 8: Access Jenkins in Browser

Open browser and go to:

```
http://<your-server-ip>:8080
```

Example:

```
http://localhost:8080
```

---

## 🔑 Step 9: Get Initial Admin Password

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Copy the password and paste it in Jenkins login screen.

---

## 🧩 Step 10: Install Suggested Plugins

After login:

- Click **Install Suggested Plugins**
- Wait for installation to complete

---

## 👤 Step 11: Create Admin User

Fill details:

- Username
- Password
- Email

Click **Save and Finish**

---

## 🎉 Jenkins Ready!

Now Jenkins is installed and ready to use for CI/CD pipelines.

---

## 🧾 Quick Summary Commands

```bash
# Install Java
sudo apt install openjdk-17-jdk -y

# Install Jenkins
sudo apt install jenkins -y

# Start Jenkins
sudo systemctl start jenkins

# Check status
sudo systemctl status jenkins

# Get admin password
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

## 🏁 End of Class 14
