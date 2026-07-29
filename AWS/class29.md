# Class 29 - Amazon EFS Mounting and Amazon Machine Image (AMI)

## Topics Covered

- Mounting Amazon EFS on an EC2 Instance
- Making the EFS Mount Permanent
- Creating an Amazon Machine Image (AMI)
- Launching a New EC2 Instance from an AMI
- Benefits and Real-Time Use Cases of AMIs

---

# 1. What is Amazon EFS?

Amazon **Elastic File System (EFS)** is a fully managed, scalable Network File System (NFS) that can be mounted simultaneously on multiple Amazon EC2 instances.

Unlike Amazon EBS, Amazon EFS is shared storage that can be accessed by multiple servers at the same time.

## Features

- Fully managed by AWS
- Shared file system
- Automatically scales storage
- Highly available across multiple Availability Zones
- Supports concurrent access from multiple EC2 instances
- Ideal for Linux-based workloads

---

# 2. Architecture

```
                +----------------------+
                |      Amazon EFS      |
                +----------------------+
                   /                \
                  /                  \
        +----------------+   +----------------+
        |   EC2 Instance 1 | |   EC2 Instance 2 |
        +----------------+   +----------------+
               |                    |
            /data                /data
```

Both EC2 instances access the same files stored in Amazon EFS.

---

# 3. Create an Amazon EFS File System

### Step 1

Navigate to

```
AWS Console
→ EFS
→ Create File System
```

Provide:

- File System Name
- VPC
- Performance Mode
- Throughput Mode

Click **Create**.

---

# 4. Configure Mount Targets

Amazon EFS creates mount targets inside the selected VPC.

Ensure:

- Mount targets are created in the required Availability Zones.
- Security Group allows NFS traffic.

---

# 5. Configure Security Group

Allow inbound rule:

| Type | Protocol | Port | Source |
|------|----------|------|--------|
| NFS | TCP | 2049 | EC2 Security Group |

This allows EC2 instances to communicate with EFS.

---

# 6. Install Amazon EFS Utilities

For Ubuntu:

```bash
sudo apt update

sudo apt install amazon-efs-utils -y
```

If the package is unavailable:

```bash
sudo apt install nfs-common -y
```

---

# 7. Create Mount Directory

Example:

```bash
sudo mkdir /data
```

---

# 8. Mount the Amazon EFS

Using EFS helper:

```bash
sudo mount -t efs fs-xxxxxxxx:/ /data
```

Or using NFS:

```bash
sudo mount -t nfs4 \
-o nfsvers=4.1 \
fs-xxxxxxxx.efs.ap-south-1.amazonaws.com:/ /data
```

Replace:

```
fs-xxxxxxxx
```

with your EFS File System ID.

---

# 9. Verify the Mount

```bash
df -h
```

Example:

```
Filesystem          Size Used Avail Use%
fs-xxxxxxxx:/       8.0E    0   8.0E   0% /data
```

Check mounted file systems:

```bash
mount | grep efs
```

---

# 10. Test Shared Storage

Create a file:

```bash
cd /data

touch test.txt

echo "Hello AWS EFS" > test.txt
```

Mount the same EFS on another EC2 instance.

Verify:

```bash
cat /data/test.txt
```

Output:

```
Hello AWS EFS
```

This confirms that both EC2 instances share the same storage.

---

# 11. Make the Mount Permanent

Edit:

```bash
sudo vi /etc/fstab
```

Add:

```
fs-xxxxxxxx:/ /data efs defaults,_netdev 0 0
```

Or using NFS:

```
fs-xxxxxxxx.efs.ap-south-1.amazonaws.com:/ /data nfs4 defaults,_netdev 0 0
```

Save the file.

Test:

```bash
sudo mount -a
```

If there are no errors, the mount is configured correctly.

---

# 12. Unmount EFS

```bash
sudo umount /data
```

Verify:

```bash
df -h
```

---

# 13. What is an Amazon Machine Image (AMI)?

An **Amazon Machine Image (AMI)** is a template used to launch EC2 instances.

It contains:

- Operating System
- Installed Software
- Packages
- System Configuration
- Applications
- EBS Snapshot information for the root volume

Using an AMI, you can quickly create multiple EC2 instances with identical configurations.

---

# 14. Why Use an AMI?

Benefits:

- Faster server provisioning
- Standardized environments
- Easy backup and recovery
- Disaster recovery
- Auto Scaling support
- Clone production servers

---

# 15. Create an AMI

### Step 1

Go to:

```
AWS Console
→ EC2
```

Select the EC2 instance.

---

### Step 2

Click:

```
Actions
→ Image and Templates
→ Create Image
```

Provide:

- Image Name
- Description

Example:

```
DevOps-Server-AMI
```

Click **Create Image**.

AWS creates:

- Snapshot(s) of attached EBS volumes
- Amazon Machine Image

---

### Step 3

Navigate to:

```
EC2
→ AMIs
```

Wait until the AMI status changes to:

```
Available
```

---

# 16. Launch a New EC2 Instance from an AMI

Go to:

```
EC2
→ AMIs
```

Select the AMI.

Click:

```
Launch Instance from AMI
```

Configure:

- Instance Name
- Instance Type
- Key Pair
- Security Group
- Storage
- VPC
- Subnet

Click **Launch Instance**.

AWS creates a new EC2 instance using the selected AMI.

---

# 17. Verify the New EC2 Instance

Connect using SSH:

```bash
ssh -i key.pem ubuntu@PUBLIC_IP
```

Verify installed software:

```bash
java -version
```

```bash
docker --version
```

```bash
nginx -v
```

Verify application files and configurations to ensure they match the source EC2 instance.

---

# 18. Important Note

An AMI copies the EC2 instance configuration and EBS-backed data, **but it does not copy the Amazon EFS file system**.

If your application uses Amazon EFS:

- Launch the EC2 instance from the AMI.
- Mount the existing EFS on the new EC2 instance.
- Both old and new EC2 instances can access the same shared files.

---

# 19. Real-Time Use Cases

## Shared Storage

- Web servers
- Kubernetes worker nodes
- Content management systems
- Shared application uploads

---

## Backup and Recovery

Create an AMI before performing operating system upgrades or major application changes.

---

## Auto Scaling

Launch identical EC2 instances automatically using a preconfigured AMI.

---

## Disaster Recovery

If an EC2 instance fails, launch a replacement instance from the AMI and reconnect it to the existing EFS.

---

## Development and Testing

Developers can launch identical environments using the same AMI while sharing common project files through EFS.

---

# 20. Common Commands

Install EFS utilities:

```bash
sudo apt update
sudo apt install amazon-efs-utils -y
```

Create mount directory:

```bash
sudo mkdir /data
```

Mount EFS:

```bash
sudo mount -t efs fs-xxxxxxxx:/ /data
```

Verify mount:

```bash
df -h
```

Display mounted file systems:

```bash
mount | grep efs
```

Unmount:

```bash
sudo umount /data
```

Test `/etc/fstab`:

```bash
sudo mount -a
```

---

# Difference Between Amazon EFS and AMI

| Amazon EFS | Amazon AMI |
|------------|------------|
| Shared network file system | Machine image template |
| Stores shared application data | Stores EC2 OS and configuration |
| Can be mounted by multiple EC2 instances | Used to launch new EC2 instances |
| Scales automatically | Does not scale storage |
| Data is shared across servers | Used for cloning server configurations |

---

# Interview Questions

### 1. What is Amazon EFS?

Amazon EFS is a fully managed shared file storage service that can be mounted simultaneously on multiple Linux EC2 instances.

---

### 2. What protocol does Amazon EFS use?

Amazon EFS uses the **NFS (Network File System)** protocol on **TCP port 2049**.

---

### 3. Can multiple EC2 instances mount the same EFS?

Yes. Multiple EC2 instances can mount the same EFS simultaneously and share the same files.

---

### 4. Does creating an AMI include Amazon EFS data?

No. An AMI includes the EC2 configuration and EBS-backed volumes only. Amazon EFS remains a separate shared storage service and must be mounted after launching a new EC2 instance.

---

### 5. Why should EFS be mounted permanently?

To ensure the file system is automatically mounted after every reboot using the `/etc/fstab` configuration.

---

# Summary

In this class, we learned how to:

- Create and configure an Amazon EFS file system.
- Mount Amazon EFS on one or more EC2 instances.
- Configure EFS for automatic mounting after reboot.
- Test shared storage between multiple EC2 instances.
- Create an Amazon Machine Image (AMI) from an existing EC2 instance.
- Launch a new EC2 instance using the AMI.
- Understand that AMIs clone EC2 configurations, while Amazon EFS provides shared storage that can be mounted by multiple EC2 instances.
