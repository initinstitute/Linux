# Class 30 - AWS IAM, Policies, Roles, EC2 IAM Role, S3 Access & Elastic Beanstalk
url: https://youtu.be/z53P5hiwOTA
## Topics Covered
- AWS IAM (Identity and Access Management)
- IAM Users
- IAM Groups
- IAM Policies
- Types of IAM Policies
- IAM Roles
- Attaching IAM Role to EC2
- Accessing S3 from EC2 using IAM Role
- IAM Security Best Practices
- IAS, PAS, SAS Concepts
- AWS Elastic Beanstalk

---

# 1. What is IAM?

IAM (Identity and Access Management) is an AWS service used to securely control access to AWS resources.

Using IAM you can:

- Create users
- Create groups
- Assign permissions
- Create roles
- Control access to AWS services
- Follow least privilege principle

IAM is a Global Service.

---

# IAM Architecture

```
                 AWS Account
                      |
        ----------------------------
        |            |            |
      Users        Groups       Roles
        |            |            |
      Policies     Policies    Policies
```

---

# 2. IAM Users

An IAM User represents an individual person or application that needs access to AWS.

Examples:

- DevOps Engineer
- Developer
- Tester
- Automation Tool

Each IAM User can have:

- Username
- Password
- Access Key
- Secret Key
- MFA

Example:

```
User:
    harish

Permissions:
    EC2
    S3
    CloudWatch
```

---

# Creating IAM User

AWS Console

```
IAM
    →
Users
    →
Create User
```

Provide

- Username
- Console access (Optional)
- Programmatic Access (CLI/API)

Attach permissions

- Existing policy
- Add to group

Create User

---

# 3. IAM Groups

A Group is a collection of IAM users.

Instead of assigning permissions individually, assign permissions to the group.

Example

```
Developers Group

    |
    |-- User1
    |-- User2
    |-- User3

Policy:
    AmazonS3ReadOnlyAccess
```

Advantages

- Easy management
- Centralized permission control
- Reduces mistakes

---

# Example Groups

```
Developers

Testers

Admins

DevOps

Finance

Support
```

---

# 4. IAM Policies

Policies are JSON documents that define permissions.

Policies specify

- Who
- What action
- Which resource
- Allow or Deny

Example

```json
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Effect":"Allow",
      "Action":"s3:*",
      "Resource":"*"
    }
  ]
}
```

---

# Policy Structure

```
Version

Statement

Effect

Action

Resource

Condition (Optional)
```

---

# Example

```
Effect

Allow

Action

ec2:StartInstances

Resource

Specific EC2
```

Meaning:

Allow user to start EC2 instance.

---

# 5. Types of IAM Policies

There are mainly two types.

## A. AWS Managed Policies

Created and maintained by AWS.

Examples

```
AmazonS3FullAccess

AmazonEC2FullAccess

AmazonRDSFullAccess

AmazonVPCFullAccess

AdministratorAccess
```

Advantages

- Ready to use
- Managed by AWS
- Updated automatically

---

## B. Customer Managed Policies

Created by customers.

You define:

- Permissions
- Resources
- Conditions

Useful when custom access is required.

---

## C. Inline Policies

Attached directly to one User, Group, or Role.

Characteristics

- One-to-one relationship
- Cannot be reused
- Deleted when identity is deleted

---

# Managed vs Inline Policies

| Managed Policy | Inline Policy |
|----------------|--------------|
| Reusable | Not reusable |
| Recommended | Special cases |
| Easier management | Hard to maintain |
| Can attach to many users | Only one identity |

---

# IAM Role

An IAM Role is a set of temporary permissions that AWS services or users can assume.

Unlike Users:

- No username
- No password
- No Access Keys

Roles provide temporary credentials.

---

# Why IAM Roles?

Used for

- EC2
- Lambda
- ECS
- EKS
- CodeBuild
- Elastic Beanstalk

Example

```
EC2

↓

Assume IAM Role

↓

Temporary Credentials

↓

Access S3
```

---

# IAM Role Example

Instead of storing

```
AWS_ACCESS_KEY

AWS_SECRET_KEY
```

inside application,

Use IAM Role.

Benefits

- Secure
- Automatic credential rotation
- No hardcoded secrets

---

# Creating IAM Role

IAM

↓

Roles

↓

Create Role

Choose trusted entity

```
AWS Service
```

Example

```
EC2
```

Attach permissions

Example

```
AmazonS3ReadOnlyAccess
```

Create Role

---

# Attaching IAM Role to EC2

EC2

↓

Instance

↓

Actions

↓

Security

↓

Modify IAM Role

Select

```
EC2-S3-Role
```

Save

Now EC2 gets temporary credentials automatically.

---

# Access S3 from EC2

Login into EC2

Install AWS CLI

```bash
aws --version
```

Check identity

```bash
aws sts get-caller-identity
```

List buckets

```bash
aws s3 ls
```

Copy files

```bash
aws s3 cp file.txt s3://mybucket/
```

Download

```bash
aws s3 cp s3://mybucket/file.txt .
```

If IAM Role has permission, no Access Key is required.

---

# Why Use IAM Role Instead of Access Keys?

❌ Old Method

```
Access Key

Secret Key

Stored inside server
```

Problems

- Can leak
- Hard to rotate
- Security risk

---

✅ Modern Method

```
EC2

↓

IAM Role

↓

Temporary Credentials

↓

S3
```

Advantages

- Secure
- Easy
- Recommended by AWS
- Automatic credential rotation

---

# IAM Best Practices

- Never use Root User for daily work
- Enable MFA
- Use IAM Groups
- Follow Least Privilege Principle
- Rotate credentials regularly
- Use IAM Roles instead of Access Keys
- Remove unused users
- Audit permissions frequently
- Enable CloudTrail for auditing

---

# Principle of Least Privilege

Give only the permissions required.

Example

Developer only needs

```
S3 Read

EC2 Describe
```

Do not give

```
AdministratorAccess
```

unless absolutely necessary.

---

# IAS, PAS, SAS

These are common cloud service models.

## 1. IaaS (Infrastructure as a Service)

Cloud provider manages:

- Networking
- Storage
- Servers
- Virtualization

Customer manages:

- OS
- Applications
- Runtime
- Data

Examples

- Amazon EC2
- Amazon EBS
- Amazon VPC

Advantages

- Full control
- Flexible
- Custom environments

---

## 2. PaaS (Platform as a Service)

Cloud provider manages:

- Infrastructure
- Operating System
- Runtime
- Middleware

Customer manages:

- Application
- Data

Examples

- AWS Elastic Beanstalk
- Google App Engine
- Azure App Service

Advantages

- Faster deployment
- Less maintenance
- Automatic scaling

---

## 3. SaaS (Software as a Service)

Cloud provider manages everything.

Customer simply uses the application.

Examples

- Gmail
- Microsoft 365
- Salesforce
- Dropbox

Advantages

- No infrastructure management
- Subscription-based
- Accessible anywhere

---

# Comparison of IaaS, PaaS and SaaS

| Feature | IaaS | PaaS | SaaS |
|----------|------|------|------|
| Infrastructure | AWS | AWS | AWS |
| Operating System | Customer | AWS | AWS |
| Runtime | Customer | AWS | AWS |
| Application | Customer | Customer | AWS |
| Data | Customer | Customer | AWS |

---

# AWS Elastic Beanstalk

Elastic Beanstalk is a Platform as a Service (PaaS) that makes it easy to deploy, manage, and scale web applications without managing the underlying infrastructure.

Supported Languages

- Java
- Python
- Node.js
- PHP
- .NET
- Go
- Ruby
- Docker

---

# Elastic Beanstalk Architecture

```
Developer

↓

Upload Application

↓

Elastic Beanstalk

↓

EC2
Auto Scaling
Load Balancer
CloudWatch
Security Groups

↓

Application Running
```

---

# Features of Elastic Beanstalk

- Easy application deployment
- Automatic provisioning of AWS resources
- Auto Scaling
- Load Balancer integration
- Health Monitoring
- Version Management
- Rolling deployments
- Easy rollback
- Supports multiple programming languages

---

# Steps to Deploy an Application Using Elastic Beanstalk

1. Open AWS Console.
2. Navigate to **Elastic Beanstalk**.
3. Click **Create Application**.
4. Enter the application name.
5. Choose the platform (Java, Python, Node.js, etc.).
6. Upload the application package (ZIP, WAR, or JAR depending on the platform).
7. Configure environment settings if required.
8. Click **Create Environment**.
9. Elastic Beanstalk provisions the required infrastructure (EC2, Load Balancer, Auto Scaling, Security Groups).
10. Access the application using the generated Elastic Beanstalk URL.

---

# Advantages of Elastic Beanstalk

- Simplifies application deployment
- Reduces infrastructure management
- Automatic scaling
- Easy updates and rollbacks
- Integrated monitoring with CloudWatch
- Cost-effective for small and medium applications
- Supports CI/CD integration

---

# Interview Questions

### 1. What is IAM?

IAM is AWS Identity and Access Management service used to securely manage access to AWS resources.

---

### 2. Difference between User and Role?

**User**
- Permanent identity
- Has login credentials
- Used by people or applications

**Role**
- Temporary identity
- No long-term credentials
- Assumed by AWS services or users

---

### 3. Why use IAM Roles for EC2?

- No Access Keys
- Temporary credentials
- Secure
- Automatic credential rotation

---

### 4. What are IAM Policy types?

- AWS Managed Policy
- Customer Managed Policy
- Inline Policy

---

### 5. What is the Least Privilege Principle?

Grant only the minimum permissions required to perform a task.

---

### 6. What is Elastic Beanstalk?

Elastic Beanstalk is an AWS PaaS service that automates application deployment, scaling, monitoring, and infrastructure provisioning.

---

# Summary

In this class, we learned:

- AWS IAM fundamentals
- IAM Users and Groups
- IAM Policies and their types
- IAM Roles and temporary credentials
- Attaching IAM Roles to EC2
- Accessing S3 securely from EC2 using IAM Roles
- IAM security best practices
- Differences between IaaS, PaaS, and SaaS
- AWS Elastic Beanstalk architecture, features, deployment steps, and benefits
