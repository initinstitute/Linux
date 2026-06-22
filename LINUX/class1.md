# AWS EC2 Instance Creation Guide

## What is an EC2 Instance?

An EC2 Instance is a virtual server (Server / Virtual Machine / Node) provided by AWS that allows you to run applications in the cloud.

---

# Step-by-Step Guide to Create an EC2 Instance

## Step 1: Log in to AWS Management Console

1. Open the AWS Management Console.
2. Sign in using your AWS account credentials.

---

## Step 2: Navigate to EC2 Service

1. Search for **EC2** in the AWS services search bar.
2. Select **EC2** from the results.

---

## Step 3: Launch an Instance

1. Click **Launch Instance**.
2. Enter a name for your instance.

---

## Step 4: Choose an Amazon Machine Image (AMI)

Select the operating system for your server:

- Amazon Linux
- Ubuntu
- Red Hat Enterprise Linux (RHEL)
- CentOS
- Debian
- Fedora
- SUSE Linux
- Oracle Linux

> **Note:** Choose a Free Tier eligible AMI if you are learning AWS.

---

## Step 5: Choose an Instance Type

Select the required instance type.

**Example:**

- `t2.micro` (Free Tier Eligible)

Click **Next** to continue.

---

## Step 6: Configure Instance Details

Configure:

- Number of instances
- Network settings
- Security settings

For beginners, the default configuration is usually sufficient.

---

## Step 7: Add Storage

Modify storage if required.

Default storage is generally enough for practice and learning purposes.

---

## Step 8: Review and Launch

Review all configurations and click **Launch Instance**.

---

## Step 9: Create or Select a Key Pair

### If you do not have a key pair:

1. Click **Create New Key Pair**.
2. Download the `.pem` file.
3. Store it safely.

⚠️ **Important:** AWS allows downloading the key only once.

### If you already have a key pair:

Select the existing key pair and continue.

---

## Step 10: View Your Instance

1. Click **View Instances**.
2. Wait for the instance status to become **Running**.

Your EC2 instance is now ready.

---

# Accessing Your EC2 Instance

## Connect to Linux EC2 Instance

Use the following SSH command:

```bash
ssh -i /path/to/your-key.pem <username>@<public-ip-address>
```

### Example

```bash
ssh -i aws-key.pem ec2-user@54.123.45.67
```

---

# Default Usernames for Different AMIs

| Operating System | Default Username |
|------------------|------------------|
| Amazon Linux | ec2-user |
| CentOS | centos / ec2-user |
| Debian | admin |
| Fedora | fedora / ec2-user |
| RHEL | ec2-user / root |
| SUSE Linux | ec2-user / root |
| Ubuntu | ubuntu |
| Oracle Linux | ec2-user |
| Bitnami | bitnami |

---

# Summary

✅ Logged into AWS Console

✅ Navigated to EC2 Service

✅ Launched an EC2 Instance

✅ Selected AMI and Instance Type

✅ Configured Storage and Security

✅ Created/Selected Key Pair

✅ Connected using SSH

---

## Daily Class Notes - Day 01

### Topics Covered

- Introduction to Cloud Computing
- AWS Overview
- AWS Global Infrastructure
- What is EC2?
- EC2 Instance Creation
- SSH Access to EC2
- Understanding Key Pairs

### Practice Task

Create:

- 1 Amazon Linux EC2 Instance
- 1 Ubuntu EC2 Instance

Connect to both instances using SSH and verify access.
