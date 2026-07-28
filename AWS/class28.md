# Class 28 - Amazon EFS (Elastic File System)
class url: https://youtu.be/f0otv2fhIjY
## Objective

In this class, we learned:

- What Amazon EFS is
- Difference between EBS and EFS
- Creating an Amazon EFS File System
- Creating Mount Targets
- Mounting EFS on multiple EC2 instances
- Making the EFS mount permanent
- Unmounting EFS
- Verifying shared storage between EC2 instances

---

# What is Amazon EFS?

Amazon Elastic File System (EFS) is a fully managed Network File System (NFS) provided by AWS.

It allows multiple EC2 instances to access the same storage simultaneously.

Unlike EBS, which can usually be attached to only one EC2 instance at a time (except Multi-Attach supported volumes), EFS is designed to be shared among multiple instances.

---

# Features of Amazon EFS

- Fully Managed Service
- Scales Automatically
- Shared File System
- Highly Available
- Highly Durable
- Supports Linux-based EC2 Instances
- Uses NFS Protocol (Version 4.1/4.0)

---

# EBS vs EFS

| Feature | Amazon EBS | Amazon EFS |
|----------|------------|------------|
| Storage Type | Block Storage | File Storage |
| Shared Access | No (except Multi-Attach supported volumes) | Yes |
| Attach To | One EC2 | Multiple EC2 Instances |
| Protocol | Block Device | NFS |
| Mount Point | Device (/dev/xvdf) | Network File System |
| Scaling | Manual | Automatic |
| Use Cases | Databases, OS disks | Shared applications, Web servers |

---

# Architecture

```
                +----------------------+
                |      Amazon EFS      |
                +----------+-----------+
                           |
          --------------------------------------
          |                                    |
    EC2 Instance 1                      EC2 Instance 2
       /mnt/efs                           /mnt/efs
          |                                    |
     Same Shared Files                  Same Shared Files
```

---

# Prerequisites

- Two EC2 Linux Instances
- Same VPC
- Same Security Group (or allow NFS traffic)
- EFS File System

---

# Step 1: Create Amazon EFS

1. Login to AWS Console
2. Open **EFS**
3. Click **Create File System**
4. Select the VPC
5. Keep default settings (or customize if needed)
6. Click **Create**

After creation, AWS automatically creates:

- File System
- Mount Targets

---

# Step 2: Configure Security Group

EFS communicates over:

```
TCP Port 2049
```

Allow:

Inbound Rule

| Type | Protocol | Port | Source |
|------|----------|------|--------|
| NFS | TCP | 2049 | EC2 Security Group |

Without this rule, EC2 cannot mount the EFS.

---

# Step 3: Install NFS Client

### Ubuntu

```bash
sudo apt update
sudo apt install nfs-common -y
```

### Amazon Linux

```bash
sudo yum install nfs-utils -y
```

---

# Step 4: Create Mount Directory

```bash
sudo mkdir /mnt/efs
```

---

# Step 5: Mount EFS

Find the DNS Name from EFS Console.

Example:

```text
fs-1234567890abcdef.efs.ap-south-1.amazonaws.com
```

Mount command:

```bash
sudo mount -t nfs4 \
fs-1234567890abcdef.efs.ap-south-1.amazonaws.com:/ \
/mnt/efs
```

Verify:

```bash
df -h
```

or

```bash
mount | grep efs
```

---

# Step 6: Mount EFS on Second EC2

Repeat the same steps on another EC2.

```bash
sudo apt update

sudo apt install nfs-common -y

sudo mkdir /mnt/efs

sudo mount -t nfs4 \
fs-1234567890abcdef.efs.ap-south-1.amazonaws.com:/ \
/mnt/efs
```

Now both EC2 instances share the same storage.

---

# Testing Shared Storage

## EC2-1

```bash
cd /mnt/efs

echo "Hello from EC2-1" > file1.txt
```

---

## EC2-2

```bash
cd /mnt/efs

cat file1.txt
```

Output

```
Hello from EC2-1
```

Now create another file.

```bash
echo "Created from EC2-2" > file2.txt
```

---

Back on EC2-1

```bash
ls
```

Output

```
file1.txt
file2.txt
```

This confirms both EC2 instances are using the same shared storage.

---

# Make EFS Mount Permanent

Temporary mounts disappear after a reboot.

To mount automatically after every reboot, edit the `/etc/fstab` file.

Open the file:

```bash
sudo vi /etc/fstab
```

Add the following line:

```text
fs-1234567890abcdef.efs.ap-south-1.amazonaws.com:/ /mnt/efs nfs4 defaults,_netdev 0 0
```

Save and exit.

Test the configuration without rebooting:

```bash
sudo mount -a
```

If there are no errors, the configuration is correct.

Verify:

```bash
df -h
```

---

# Verify After Reboot

Reboot the instance:

```bash
sudo reboot
```

After login:

```bash
df -h
```

or

```bash
mount | grep efs
```

The EFS should be mounted automatically.

---

# Unmount EFS

Unmount the EFS:

```bash
sudo umount /mnt/efs
```

Verify:

```bash
df -h
```

The mount point should no longer appear.

---

# Remove Permanent Mount

If you no longer want EFS to mount automatically:

Open:

```bash
sudo vi /etc/fstab
```

Remove or comment out the EFS entry:

```text
# fs-1234567890abcdef.efs.ap-south-1.amazonaws.com:/ /mnt/efs nfs4 defaults,_netdev 0 0
```

Save the file.

---

# Useful Commands

Create directory

```bash
sudo mkdir /mnt/efs
```

Mount EFS

```bash
sudo mount -t nfs4 EFS_DNS_NAME:/ /mnt/efs
```

Check mounts

```bash
mount
```

Disk usage

```bash
df -h
```

List files

```bash
ls -l /mnt/efs
```

Unmount

```bash
sudo umount /mnt/efs
```

Reload fstab

```bash
sudo mount -a
```

---

# Advantages of Amazon EFS

- Shared storage for multiple EC2 instances
- Fully managed by AWS
- Automatically scales storage
- High availability across Availability Zones
- No manual storage resizing
- Easy backup with AWS Backup
- Supports concurrent access from many clients

---

# Common Use Cases

- Shared application files
- Web server content
- CMS platforms (WordPress, Drupal, Joomla)
- Home directories
- Machine Learning datasets
- Container persistent storage (EKS/ECS)
- Media processing
- Dev/Test environments

---

# Best Practices

- Allow only required Security Groups on port **2049**.
- Always use `/etc/fstab` for permanent mounts.
- Use `_netdev` in the fstab entry to ensure the network is available before mounting.
- Monitor EFS usage with **Amazon CloudWatch**.
- Enable automatic backups using **AWS Backup** for data protection.

---

# Summary

In this class, we covered:

- Understanding Amazon EFS
- Difference between EBS and EFS
- Creating an EFS File System
- Configuring Security Groups
- Installing NFS client
- Mounting EFS on EC2
- Sharing the same EFS across two EC2 instances
- Testing shared file access
- Making the mount permanent using `/etc/fstab`
- Verifying automatic mounting after reboot
- Unmounting EFS
- Removing the permanent mount configuration
- Best practices and real-world use cases
