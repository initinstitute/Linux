# Class 27 - AWS EBS (Elastic Block Store)
class url: https://youtu.be/jOgG2_Ijh-I
## Topics Covered

1. Creating an EBS Volume
2. Attaching an EBS Volume to an EC2 Instance
3. Formatting and Mounting the Volume
4. Making the EBS Volume Mount Permanently
5. Creating a Snapshot from an EBS Volume
6. Restoring a Volume from a Snapshot
7. Best Practices

---

# What is Amazon EBS?

Amazon Elastic Block Store (EBS) is a block storage service that provides persistent storage for Amazon EC2 instances.

### Features

- Persistent storage
- High durability and availability
- Supports snapshots
- Can be resized
- Encryptable
- Suitable for databases and applications

---

# EBS Volume Types

| Volume Type | Description | Use Case |
|-------------|-------------|----------|
| gp3 | General Purpose SSD | Most workloads |
| gp2 | Previous generation SSD | General workloads |
| io1/io2 | Provisioned IOPS SSD | High-performance databases |
| st1 | Throughput Optimized HDD | Big data |
| sc1 | Cold HDD | Archive storage |

---

# Architecture

```
EC2 Instance
     │
     │
     ▼
+-----------------+
|   EBS Volume    |
+-----------------+
     │
     │ Snapshot
     ▼
Amazon S3 (Managed by AWS)
```

---

# Step 1: Create an EBS Volume

## AWS Console

Navigate to:

```
AWS Console
    ↓
EC2
    ↓
Elastic Block Store
    ↓
Volumes
    ↓
Create Volume
```

Fill the details:

- Volume Type → gp3
- Size → 10 GB
- Availability Zone → Same as EC2 Instance
- Encryption → Optional

Click

```
Create Volume
```

---

# Step 2: Attach the Volume

Select the volume

```
Actions
    ↓
Attach Volume
```

Choose

- EC2 Instance
- Device Name

Example

```
/dev/sdf
```

Click

```
Attach Volume
```

---

# Step 3: Verify the Volume

Login to EC2

List block devices

```bash
lsblk
```

Example

```
NAME
nvme0n1
└── Root Volume

nvme1n1
```

Check disk information

```bash
sudo fdisk -l
```

---

# Step 4: Create Filesystem

Format the new disk

```bash
sudo mkfs.ext4 /dev/nvme1n1
```

Verify

```bash
sudo file -s /dev/nvme1n1
```

Expected Output

```
ext4 filesystem
```

---

# Step 5: Create Mount Directory

```bash
sudo mkdir /data1
```

---

# Step 6: Mount the Volume

```bash
sudo mount /dev/nvme1n1 /data1
```

Verify

```bash
df -h
```

Example

```
Filesystem      Mounted on
/dev/nvme1n1    /data1
```

---

# Step 7: Test the Volume

```bash
cd /data1
```

Create a file

```bash
touch demo.txt
```

Verify

```bash
ls
```

---

# Step 8: Make the Mount Permanent

## Get UUID

```bash
sudo blkid
```

Example

```
UUID="4f94abf3-8ef9-41ea"
```

Copy the UUID.

---

Open fstab

```bash
sudo nano /etc/fstab
```

Add

```text
UUID=<UUID>   /data1   ext4   defaults,nofail   0   2
```

Example

```text
UUID=4f94abf3-8ef9-41ea   /data1   ext4   defaults,nofail   0   2
```

Save

```
CTRL + O
Enter

CTRL + X
```

---

## Test fstab

Unmount

```bash
sudo umount /data1
```

Mount all entries

```bash
sudo mount -a
```

Verify

```bash
df -h
```

If there are no errors, the mount will also work after every reboot.

---

# Important Commands

Check mounted disks

```bash
df -h
```

Check block devices

```bash
lsblk
```

Check filesystem

```bash
blkid
```

Disk information

```bash
sudo fdisk -l
```

Check filesystem type

```bash
sudo file -s /dev/nvme1n1
```

Unmount

```bash
sudo umount /data1
```

Mount manually

```bash
sudo mount /dev/nvme1n1 /data1
```

---

# Creating an EBS Snapshot

## What is a Snapshot?

A snapshot is a backup of an EBS volume. AWS stores snapshots incrementally in Amazon S3 (managed by AWS), which helps reduce storage costs.

### Benefits

- Backup
- Disaster Recovery
- Restore deleted volumes
- Create new volumes
- Migrate data across Availability Zones or Regions (via copy)

---

# Steps to Create a Snapshot

Navigate to

```
AWS Console
    ↓
EC2
    ↓
Elastic Block Store
    ↓
Volumes
```

Select the EBS Volume

```
Actions
    ↓
Create Snapshot
```

Enter

- Name
- Description
- Tags (Optional)

Click

```
Create Snapshot
```

Wait until the snapshot state changes to

```
Completed
```

---

# View Snapshots

Navigate to

```
EC2
    ↓
Snapshots
```

You can view

- Snapshot ID
- Size
- State
- Creation Time
- Volume ID

---

# Create a New Volume from a Snapshot

Navigate to

```
EC2
    ↓
Snapshots
```

Select the snapshot

```
Actions
    ↓
Create Volume from Snapshot
```

Choose

- Availability Zone
- Volume Type
- Size

Click

```
Create Volume
```

Attach the newly created volume to an EC2 instance using the same process as before.

---

# Best Practices

- Always create snapshots before making major system changes.
- Keep snapshots with meaningful names and tags.
- Use gp3 volumes for most workloads.
- Delete unused volumes and snapshots to reduce costs.
- Ensure the EBS volume and EC2 instance are in the same Availability Zone.
- Use UUID in `/etc/fstab` instead of device names for reliable mounting.
- Regularly monitor EBS utilization and storage costs using Amazon CloudWatch.

---

# Summary

- Created an EBS Volume
- Attached it to an EC2 instance
- Formatted the volume
- Mounted the volume
- Made the mount persistent using `/etc/fstab`
- Created an EBS Snapshot
- Restored a new EBS Volume from a Snapshot
- Learned EBS best practices

---

# Interview Questions

### 1. What is Amazon EBS?

Amazon EBS is a persistent block storage service for Amazon EC2 instances.

---

### 2. Can one EBS volume be attached to multiple EC2 instances?

Normally, no. Multi-Attach is supported only for specific Provisioned IOPS (`io1`/`io2`) volumes and compatible instance types.

---

### 3. What is the difference between EBS and Instance Store?

| EBS | Instance Store |
|------|----------------|
| Persistent | Temporary |
| Supports snapshots | No snapshots |
| Data survives stop/start | Data is lost when the instance is stopped or terminated (depending on instance type) |
| Network-attached storage | Physically attached storage |

---

### 4. What is a Snapshot?

A point-in-time backup of an EBS volume stored incrementally by AWS.

---

### 5. Why do we use `/etc/fstab`?

To automatically mount file systems during system boot.

---

### 6. Why use UUID instead of device names?

Device names (for example, `/dev/nvme1n1`) can change after a reboot. UUIDs remain consistent, ensuring reliable mounting.
