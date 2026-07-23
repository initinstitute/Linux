# class26.md

class Url: https://youtu.be/NtIsIZKSVmM
# AWS Cloud Fundamentals

## What is Cloud Computing?

Cloud Computing is the on-demand delivery of IT resources such as compute, storage, networking, databases, analytics, and software over the internet with pay-as-you-go pricing.

Instead of purchasing and maintaining physical servers, organizations can rent resources from cloud providers like AWS, Microsoft Azure, and Google Cloud Platform (GCP).

### Definition

> **Cloud Computing** is the practice of using remote servers hosted on the internet to store, manage, and process data instead of using local servers or personal computers.

---

# Why Cloud Computing?

Traditional infrastructure requires organizations to:

- Purchase expensive hardware.
- Build and maintain data centers.
- Hire infrastructure teams.
- Plan capacity in advance.
- Handle hardware failures.
- Upgrade systems manually.

Cloud computing eliminates most of these challenges by providing resources on demand.

---

# Advantages of Cloud Computing

- Pay only for what you use.
- No upfront hardware investment.
- High availability.
- Scalability.
- Global infrastructure.
- Better security.
- Faster deployment.
- Automatic backups.
- Disaster recovery support.
- Managed services.

---

# Cloud Service Models

## 1. Infrastructure as a Service (IaaS)

The cloud provider manages the infrastructure while the customer manages the operating system and applications.

Examples:

- Amazon EC2
- Amazon EBS
- Amazon VPC

---

## 2. Platform as a Service (PaaS)

The cloud provider manages the operating system and runtime environment.

Examples:

- AWS Elastic Beanstalk
- AWS Lambda
- AWS App Runner

---

## 3. Software as a Service (SaaS)

Applications are delivered directly to end users through the internet.

Examples:

- Gmail
- Microsoft 365
- Salesforce

---

# Cloud Deployment Models

- Public Cloud
- Private Cloud
- Hybrid Cloud
- Multi Cloud

---

# Real-Time Use Cases of Cloud

## 1. Website Hosting

Host websites without purchasing physical servers.

Example:

- Company websites
- E-commerce websites

---

## 2. Application Hosting

Deploy Java, Python, Node.js, or .NET applications on EC2 instances.

---

## 3. Backup and Disaster Recovery

Store backups securely in S3 and recover data during failures.

---

## 4. Data Storage

Store images, videos, documents, and application data.

Example:

- Netflix
- Instagram
- Amazon

---

## 5. Big Data Analytics

Analyze petabytes of data using AWS analytics services.

---

## 6. Machine Learning

Train AI models without purchasing expensive GPUs.

---

## 7. DevOps & CI/CD

Run Jenkins, Docker, Kubernetes, Terraform, and Ansible on AWS.

---

## 8. Database Hosting

Deploy databases like:

- MySQL
- PostgreSQL
- MongoDB
- Oracle

---

## 9. Content Delivery

Deliver videos and static content globally using Amazon CloudFront.

---

# What is Amazon EC2?

Amazon EC2 (Elastic Compute Cloud) is a virtual server in AWS.

It allows users to launch Linux or Windows virtual machines within minutes.

Common uses:

- Web servers
- Application servers
- Jenkins servers
- Database servers
- Kubernetes nodes
- Docker hosts

---

# EC2 Instance Families

AWS provides different EC2 instance families based on workload requirements.

---

# 1. General Purpose (T, M)

Balanced CPU, memory, and networking.

Examples:

- t2.micro
- t3.medium
- t3.large
- m5.large
- m6i.large

Use Cases:

- Web applications
- Small databases
- Development servers
- Jenkins servers

---

# 2. Compute Optimized (C)

High CPU performance.

Examples:

- c5.large
- c6i.large

Use Cases:

- Gaming servers
- Video encoding
- Scientific computing
- High-performance applications

---

# 3. Memory Optimized (R, X)

High RAM.

Examples:

- r5.large
- r6i.large
- x1e.large

Use Cases:

- SAP HANA
- Redis
- Large databases
- Analytics

---

# 4. Storage Optimized (I, D)

High local storage performance.

Examples:

- i3.large
- d2.large

Use Cases:

- Data warehousing
- Hadoop
- Elasticsearch
- NoSQL databases

---

# 5. Accelerated Computing (P, G, F)

GPU-based instances.

Examples:

- p3
- p4
- g5

Use Cases:

- Machine Learning
- AI
- Video rendering
- Deep learning

---

# AWS EC2 Purchasing Options

AWS provides multiple purchasing models to optimize costs.

---

## 1. On-Demand Instances

Pay per second or per hour with no long-term commitment.

### Advantages

- No upfront payment
- Flexible
- Best for testing and development

### Use Cases

- Learning AWS
- Temporary workloads
- Development
- Short-term projects

---

## 2. Reserved Instances (RI)

Reserve capacity for 1 or 3 years in exchange for significant discounts.

### Advantages

- Up to 72% cheaper than On-Demand
- Predictable costs

### Use Cases

- Production workloads
- Long-running applications

---

## 3. Savings Plans

Commit to a consistent amount of usage (per hour) for 1 or 3 years.

### Advantages

- Flexible across instance families and regions (depending on plan)
- Up to 72% savings
- Easier to manage than Reserved Instances

### Types

- Compute Savings Plan
- EC2 Instance Savings Plan

### Use Cases

- Organizations with steady compute usage

---

## 4. Spot Instances

Use AWS's unused EC2 capacity at a large discount.

### Advantages

- Up to 90% cheaper

### Disadvantages

- AWS can reclaim the instance with short notice.

### Use Cases

- Batch processing
- CI/CD jobs
- Testing
- Big data
- Rendering

---

## 5. Dedicated Hosts

A physical server dedicated to a single customer.

### Use Cases

- Compliance requirements
- License-bound software
- Enterprise workloads

---

# Cost Optimization Tips

- Use Spot Instances for non-critical workloads.
- Use Savings Plans for predictable workloads.
- Use Reserved Instances for long-running production servers.
- Stop unused EC2 instances.
- Delete unattached EBS volumes.
- Use Auto Scaling to reduce idle resources.
- Monitor usage with AWS Cost Explorer and AWS Budgets.
- Select the right instance family and size.

---

# AWS Storage Services

AWS provides different storage services for different use cases.

The three most commonly used storage services are:

- Amazon EBS
- Amazon EFS
- Amazon S3

---

# Amazon EBS (Elastic Block Store)

Amazon EBS provides block-level storage volumes that are attached to EC2 instances.

It works like a virtual hard disk.

### Features

- Persistent storage
- High performance
- Snapshots to S3
- Encryption support
- Resize volumes
- Low latency

### Use Cases

- Operating systems
- Databases
- Boot volumes
- Enterprise applications

---

# EBS Volume Types

- gp3 (General Purpose SSD)
- gp2 (Previous Generation SSD)
- io1/io2 (Provisioned IOPS SSD)
- st1 (Throughput Optimized HDD)
- sc1 (Cold HDD)

---

# Amazon EFS (Elastic File System)

Amazon EFS is a fully managed Network File System (NFS).

Multiple EC2 instances can mount the same file system simultaneously.

### Features

- Shared storage
- Automatic scaling
- Highly available
- Managed service
- Supports Linux NFS clients

### Use Cases

- Shared application files
- Web servers
- Content management systems
- Container storage
- Analytics workloads

---

# Amazon S3 (Simple Storage Service)

Amazon S3 is an object storage service designed for massive scalability and durability.

Data is stored as objects inside buckets.

### Features

- 99.999999999% (11 9's) durability
- Unlimited storage
- Versioning
- Lifecycle policies
- Cross-region replication
- Encryption
- Static website hosting

### Use Cases

- Image storage
- Video storage
- Application backups
- Static websites
- Log storage
- Data lakes

---

# S3 Storage Classes

- Standard
- Intelligent-Tiering
- Standard-IA
- One Zone-IA
- Glacier Instant Retrieval
- Glacier Flexible Retrieval
- Glacier Deep Archive

---

# Difference Between EBS, EFS, and S3

| Feature | EBS | EFS | S3 |
|----------|-----|-----|----|
| Storage Type | Block Storage | File Storage | Object Storage |
| Used With | Single EC2 Instance (can be reattached when detached) | Multiple EC2 Instances | Internet, AWS Services |
| Access Method | Mounted as a disk | Mounted using NFS | API, SDK, CLI, Console |
| Scalability | Manual resize | Automatic | Virtually Unlimited |
| Shared Storage | No | Yes | Yes (via object access) |
| Performance | Very High | High | High |
| Operating System Files | Yes | No | No |
| Database Storage | Yes | No | No |
| Static Website Hosting | No | No | Yes |
| Backup Storage | Limited | Limited | Excellent |
| Maximum Size | Up to 64 TiB per volume | Petabyte scale (elastic) | Virtually Unlimited |
| Typical Use Cases | Boot volumes, databases, applications | Shared file systems, web servers, containers | Images, videos, backups, logs, archives |

---

# Choosing the Right Storage

| Requirement | Recommended Storage |
|-------------|---------------------|
| EC2 Boot Volume | EBS |
| MySQL/PostgreSQL Database | EBS |
| Shared Files Across Multiple EC2 Instances | EFS |
| Kubernetes Shared Persistent Storage | EFS |
| Store Images and Videos | S3 |
| Backup Files | S3 |
| Archive Data | S3 Glacier |
| Static Website Hosting | S3 |
| Application Logs | S3 |

---

# Interview Questions

## 1. What is Cloud Computing?

Cloud computing is the on-demand delivery of computing services such as servers, storage, networking, databases, and software over the internet with pay-as-you-go pricing.

---

## 2. What are the different EC2 instance families?

- General Purpose
- Compute Optimized
- Memory Optimized
- Storage Optimized
- Accelerated Computing

---

## 3. What are the EC2 purchasing options?

- On-Demand
- Reserved Instances
- Savings Plans
- Spot Instances
- Dedicated Hosts

---

## 4. What is the difference between EBS, EFS, and S3?

- **EBS** is block storage attached to an EC2 instance and is commonly used for operating systems and databases.
- **EFS** is a shared file system that multiple EC2 instances can mount simultaneously.
- **S3** is an object storage service designed for storing files, backups, logs, images, videos, and static website content.

---

## 5. Which AWS storage service would you choose for the following?

| Scenario | Service |
|----------|---------|
| Linux Boot Disk | EBS |
| Shared Application Files | EFS |
| Backup Storage | S3 |
| Static Website | S3 |
| Database | EBS |
| Archive Data | S3 Glacier |
