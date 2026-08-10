# Class 34 – Amazon S3
class url: https://youtu.be/z6LTstW5E1g
## 1. What is Amazon S3?

**Amazon Simple Storage Service (Amazon S3)** is an AWS object storage service used to store and retrieve any amount of data from anywhere over the internet.

S3 is commonly used for:

* Backup and restore
* Static website hosting
* Application files
* Images and videos
* Log files
* Database backups
* Data lakes
* Software packages
* Terraform state files
* CI/CD artifacts

### Important Characteristics

* Highly scalable
* Highly durable
* Available globally through AWS Regions
* Object-based storage
* Supports versioning
* Supports lifecycle management
* Supports encryption
* Supports access control through policies
* Can be accessed using AWS Console, CLI, SDKs, and APIs

---

# 2. S3 Basic Architecture

The basic structure of S3 is:

```text
AWS Account
    |
    +---- S3
          |
          +---- Bucket
                 |
                 +---- Object
                 |
                 +---- Object
                 |
                 +---- Folder/
                        |
                        +---- Object
```

### Example

```text
Bucket: my-devops-bucket

my-devops-bucket/
├── images/
│   ├── image1.jpg
│   └── image2.jpg
│
├── backups/
│   ├── db-backup.sql
│   └── app-backup.tar.gz
│
└── index.html
```

---

# 3. S3 Bucket

A **bucket** is a container used to store objects in Amazon S3.

Before uploading any object to S3, we need a bucket.

### Example

```text
Bucket Name:

my-devops-training-bucket
```

Inside the bucket we can store:

```text
my-devops-training-bucket/
├── file1.txt
├── file2.txt
├── images/
├── backups/
└── logs/
```

---

# 4. S3 Object

An **object** is the actual data stored inside an S3 bucket.

An object consists of:

* Object data
* Object key
* Metadata
* Tags
* Version ID (when versioning is enabled)

### Example

```text
Bucket:
my-devops-training-bucket

Object:
backup/database.sql
```

Here:

```text
Bucket = my-devops-training-bucket

Object Key = backup/database.sql
```

---

# 5. S3 Object Key

S3 does not actually have traditional folders like a Linux filesystem.

For example:

```text
backup/database.sql
```

is an **object key**.

The `/` character is used to provide a folder-like structure.

Example:

```text
my-bucket/
├── dev/
│   ├── app.jar
│   └── config.txt
│
└── prod/
    ├── app.jar
    └── config.txt
```

Actual object keys:

```text
dev/app.jar
dev/config.txt
prod/app.jar
prod/config.txt
```

---

# 6. S3 Bucket Naming Rules

S3 bucket names must follow specific naming requirements.

Important rules:

* Bucket names must be globally unique.
* Use lowercase letters.
* Numbers are allowed.
* Hyphens are commonly used.
* Avoid spaces and uppercase characters.
* Bucket names generally need to be DNS-compatible.

### Example

Valid:

```text
my-devops-bucket-2026
```

Invalid examples:

```text
My DevOps Bucket
my_devops_bucket
```

---

# 7. Creating an S3 Bucket Using AWS Console

### Step 1

Login to AWS Management Console.

### Step 2

Search for:

```text
S3
```

### Step 3

Select:

```text
S3
```

### Step 4

Click:

```text
Create bucket
```

### Step 5

Enter a unique bucket name.

Example:

```text
my-devops-training-bucket-2026
```

### Step 6

Select the required AWS Region.

Example:

```text
Asia Pacific (Mumbai)
ap-south-1
```

### Step 7

Configure:

* Object Ownership
* Block Public Access
* Bucket Versioning
* Tags
* Default Encryption
* Advanced settings

### Step 8

Click:

```text
Create bucket
```

---

# 8. Uploading Files to S3

From the AWS Console:

```text
S3
  |
  +---- Bucket
          |
          +---- Upload
```

Select:

```text
Add files
```

or

```text
Add folder
```

Then click:

```text
Upload
```

---

# 9. S3 Storage Classes

Amazon S3 provides different storage classes based on access frequency and cost requirements.

Common storage classes:

| Storage Class                 | Use Case                                 |
| ----------------------------- | ---------------------------------------- |
| S3 Standard                   | Frequently accessed data                 |
| S3 Intelligent-Tiering        | Data with changing access patterns       |
| S3 Standard-IA                | Infrequently accessed data               |
| S3 One Zone-IA                | Infrequently accessed, re-creatable data |
| S3 Glacier Instant Retrieval  | Archive data requiring fast retrieval    |
| S3 Glacier Flexible Retrieval | Long-term archive                        |
| S3 Glacier Deep Archive       | Very long-term archive                   |

### Example

Frequently accessed application files:

```text
S3 Standard
```

Long-term database backups:

```text
S3 Glacier
```

Very rarely accessed compliance data:

```text
S3 Glacier Deep Archive
```

---

# 10. S3 Versioning

S3 Versioning keeps multiple versions of an object.

Example:

```text
app.zip
```

First upload:

```text
Version 1
```

Second upload:

```text
Version 2
```

Third upload:

```text
Version 3
```

Instead of permanently replacing the previous versions, S3 can retain them.

### Benefits

* Recover accidentally deleted objects
* Recover previous versions
* Protect against accidental overwrites
* Useful for backups

### Enable Versioning

```text
S3
  |
  +---- Bucket
          |
          +---- Properties
                  |
                  +---- Bucket Versioning
```

Enable:

```text
Enable
```

---

# 11. S3 Encryption

S3 supports encryption for protecting data at rest.

Common options include:

* SSE-S3
* SSE-KMS
* DSSE-KMS
* Client-side encryption

### SSE-S3

AWS manages the encryption keys.

```text
User
 |
 v
S3
 |
 +---- Encrypt Object
```

### SSE-KMS

AWS KMS is used for key management.

```text
User
 |
 v
S3
 |
 v
AWS KMS
 |
 v
Encryption Key
```

---

# 12. S3 Bucket Policy

A **bucket policy** is a resource-based JSON policy attached directly to an S3 bucket.

It controls who can access the bucket and what actions they can perform.

Bucket policies are written in **JSON**.

---

# 13. Why Do We Need Bucket Policies?

Bucket policies can be used to:

* Allow access to specific AWS principals
* Deny access
* Allow access from specific IP addresses
* Allow another AWS account to access a bucket
* Restrict access to HTTPS
* Control specific S3 actions

---

# 14. Bucket Policy Structure

A typical bucket policy contains:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

Important fields:

| Field     | Meaning                          |
| --------- | -------------------------------- |
| Version   | Policy language version          |
| Statement | Contains policy statements       |
| Effect    | Allow or Deny                    |
| Principal | Who is affected                  |
| Action    | What operation is allowed/denied |
| Resource  | Which AWS resource is affected   |
| Condition | Optional restrictions            |

---

# 15. Effect

The `Effect` specifies whether an action is allowed or denied.

### Allow

```json
"Effect": "Allow"
```

### Deny

```json
"Effect": "Deny"
```

---

# 16. Principal

`Principal` specifies who can access the resource.

Example:

```json
"Principal": "*"
```

means everyone.

Specific AWS account:

```json
"Principal": {
  "AWS": "arn:aws:iam::123456789012:root"
}
```

Specific IAM role:

```json
"Principal": {
  "AWS": "arn:aws:iam::123456789012:role/MyRole"
}
```

---

# 17. S3 Actions

Common S3 actions include:

```text
s3:ListBucket
s3:GetObject
s3:PutObject
s3:DeleteObject
s3:GetBucketLocation
```

### Get Object

```text
s3:GetObject
```

Used to download/read an object.

### Put Object

```text
s3:PutObject
```

Used to upload an object.

### Delete Object

```text
s3:DeleteObject
```

Used to delete an object.

### List Bucket

```text
s3:ListBucket
```

Used to list objects in a bucket.

---

# 18. S3 ARN

Amazon Resource Names identify AWS resources.

### Bucket ARN

```text
arn:aws:s3:::my-bucket
```

### Object ARN

```text
arn:aws:s3:::my-bucket/*
```

Example:

```text
arn:aws:s3:::my-devops-bucket/*
```

The `/*` represents objects inside the bucket.

---

# 19. Example Bucket Policy – Read Objects

Example policy allowing object read access:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowReadObjects",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-devops-bucket/*"
    }
  ]
}
```

> Be careful when using `"Principal": "*"` because it can make objects publicly accessible. Public access should only be enabled intentionally and with appropriate controls.

---

# 20. Bucket Policy vs IAM Policy

| Feature              | IAM Policy             | Bucket Policy  |
| -------------------- | ---------------------- | -------------- |
| Type                 | Identity-based         | Resource-based |
| Attached to          | IAM users/groups/roles | S3 bucket      |
| Controls access      | Yes                    | Yes            |
| JSON                 | Yes                    | Yes            |
| Cross-account access | Possible               | Commonly used  |
| Resource specified   | Usually                | Yes            |

### Example

IAM Policy:

```text
IAM User
   |
   +---- IAM Policy
           |
           +---- S3 permissions
```

Bucket Policy:

```text
S3 Bucket
   |
   +---- Bucket Policy
           |
           +---- Principal permissions
```

---

# 21. S3 Block Public Access

S3 provides **Block Public Access** settings to help prevent unintended public access.

Recommended approach:

```text
Block Public Access = Enabled
```

unless public access is specifically required.

For most private application buckets:

```text
Block Public Access
        |
        +---- Enabled
```

---

# 22. S3 Lifecycle Management

An **S3 Lifecycle configuration** automatically performs actions on objects based on rules.

It is mainly used to:

* Reduce storage costs
* Move objects to cheaper storage classes
* Delete old objects
* Delete incomplete multipart uploads
* Manage old object versions

---

# 23. Why Use S3 Lifecycle?

Suppose an application creates daily backups:

```text
backup-01.sql
backup-02.sql
backup-03.sql
...
backup-365.sql
```

We may want:

```text
0-30 days
    |
    +---- S3 Standard

31-90 days
    |
    +---- S3 Standard-IA

91+ days
    |
    +---- Glacier

365+ days
    |
    +---- Delete
```

This can reduce storage costs automatically.

---

# 24. Lifecycle Rule Example

Example lifecycle:

```text
Day 0
 |
 +---- S3 Standard
 |
Day 30
 |
 +---- S3 Standard-IA
 |
Day 90
 |
 +---- S3 Glacier
 |
Day 365
 |
 +---- Delete
```

---

# 25. Lifecycle Rule Components

A lifecycle rule can contain:

* Rule ID
* Status
* Filter
* Transition actions
* Expiration actions
* Noncurrent version transitions
* Noncurrent version expiration
* Abort incomplete multipart uploads

---

# 26. Lifecycle Transition

Transition moves objects from one storage class to another.

Example:

```text
S3 Standard
     |
     | 30 days
     v
S3 Standard-IA
     |
     | 60 days
     v
S3 Glacier
```

---

# 27. Lifecycle Expiration

Expiration automatically deletes objects after a specified period.

Example:

```text
Object created
      |
      | 365 days
      v
Delete object
```

Useful for:

* Temporary files
* Logs
* Old backups
* Temporary application artifacts

---

# 28. Lifecycle Prefix/Filter

Lifecycle rules can be applied to specific objects.

Example:

```text
backups/
```

Only objects under:

```text
backups/
```

are affected.

Example:

```text
my-bucket/
├── backups/
│   ├── db1.sql
│   └── db2.sql
│
└── application/
    ├── app.jar
    └── config.txt
```

A lifecycle rule for:

```text
backups/
```

will apply to:

```text
backups/db1.sql
backups/db2.sql
```

but not:

```text
application/app.jar
```

---

# 29. Example Lifecycle Policy Concept

```text
Rule: Backup Lifecycle

Filter:
backups/

Transitions:
After 30 days  -> Standard-IA
After 90 days  -> Glacier

Expiration:
After 365 days -> Delete
```

---

# 30. AWS CLI for S3

AWS CLI can be used to manage S3 from the command line.

Basic syntax:

```bash
aws s3 <command>
```

Examples:

```bash
aws s3 ls
aws s3 mb
aws s3 cp
aws s3 sync
aws s3 rm
```

---

# 31. Configure AWS CLI

Before using AWS CLI, configure credentials and default settings.

```bash
aws configure
```

It asks for:

```text
AWS Access Key ID
AWS Secret Access Key
Default region name
Default output format
```

Example:

```text
AWS Access Key ID: ********
AWS Secret Access Key: ********
Default region name: ap-south-1
Default output format: json
```

> Prefer IAM roles, IAM Identity Center, or other secure credential mechanisms when possible instead of storing long-lived access keys.

---

# 32. Check AWS Identity

To verify which AWS identity the CLI is using:

```bash
aws sts get-caller-identity
```

Example output:

```json
{
    "UserId": "AIDAEXAMPLE",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/devops"
}
```

If running on an EC2 instance with an IAM role, the ARN can refer to the assumed role.

---

# 33. List S3 Buckets

Command:

```bash
aws s3 ls
```

Example:

```text
2026-08-10  my-devops-bucket
2026-08-10  project-backup-bucket
```

---

# 34. List Objects Inside a Bucket

Command:

```bash
aws s3 ls s3://my-devops-bucket
```

Example:

```bash
aws s3 ls s3://my-devops-bucket
```

---

# 35. List Objects Recursively

Command:

```bash
aws s3 ls s3://my-devops-bucket --recursive
```

This displays objects inside subfolders as well.

Example:

```text
2026-08-10  1000  app.jar
2026-08-10  2000  config/app.conf
2026-08-10  5000  backup/db.sql
```

---

# 36. AWS S3 `cp` Command

The `cp` command is used to copy files and folders between:

```text
Local -> S3
S3 -> Local
S3 -> S3
```

General syntax:

```bash
aws s3 cp <source> <destination>
```

---

# 37. Copy Local File to S3

Suppose we have:

```text
/home/ubuntu/app.jar
```

Upload it:

```bash
aws s3 cp /home/ubuntu/app.jar s3://my-devops-bucket/
```

The object will be stored as:

```text
s3://my-devops-bucket/app.jar
```

---

# 38. Copy File to an S3 Folder

```bash
aws s3 cp app.jar s3://my-devops-bucket/application/
```

Result:

```text
s3://my-devops-bucket/application/app.jar
```

---

# 39. Copy S3 File to Local Machine

Command:

```bash
aws s3 cp s3://my-devops-bucket/app.jar .
```

`.` means the current directory.

Example:

```bash
aws s3 cp s3://my-devops-bucket/app.jar /home/ubuntu/
```

---

# 40. Copy S3 File to Another Local Location

```bash
aws s3 cp s3://my-devops-bucket/app.jar /tmp/app.jar
```

---

# 41. Copy File from One S3 Bucket to Another

Command:

```bash
aws s3 cp s3://source-bucket/app.jar s3://destination-bucket/
```

Example:

```bash
aws s3 cp s3://dev-bucket/app.jar s3://prod-bucket/
```

This copies:

```text
dev-bucket/app.jar
        |
        v
prod-bucket/app.jar
```

---

# 42. Copy a Folder from Local to S3

Suppose:

```text
application/
├── app.jar
├── config.txt
└── README.md
```

Command:

```bash
aws s3 cp application/ s3://my-devops-bucket/application/ --recursive
```

`--recursive` is required when copying the contents of a directory.

---

# 43. Copy Folder from S3 to Local

Command:

```bash
aws s3 cp s3://my-devops-bucket/application/ ./application/ --recursive
```

This downloads all objects under:

```text
application/
```

---

# 44. Copy Folder from One S3 Bucket to Another

Command:

```bash
aws s3 cp s3://source-bucket/application/ s3://destination-bucket/application/ --recursive
```

---

# 45. `cp` Command Summary

### Local file → S3

```bash
aws s3 cp file.txt s3://my-bucket/
```

### S3 → Local

```bash
aws s3 cp s3://my-bucket/file.txt .
```

### S3 → S3

```bash
aws s3 cp s3://source-bucket/file.txt s3://destination-bucket/
```

### Local folder → S3

```bash
aws s3 cp folder/ s3://my-bucket/folder/ --recursive
```

### S3 folder → Local

```bash
aws s3 cp s3://my-bucket/folder/ ./folder/ --recursive
```

### S3 folder → S3 folder

```bash
aws s3 cp s3://source-bucket/folder/ s3://destination-bucket/folder/ --recursive
```

---

# 46. AWS S3 `sync` Command

The `sync` command synchronizes files between:

```text
Local -> S3
S3 -> Local
S3 -> S3
```

General syntax:

```bash
aws s3 sync <source> <destination>
```

---

# 47. Local Folder to S3 Using `sync`

Suppose:

```text
website/
├── index.html
├── css/
│   └── style.css
└── images/
    └── logo.png
```

Command:

```bash
aws s3 sync website/ s3://my-devops-bucket/website/
```

It synchronizes the local directory with the S3 location.

---

# 48. S3 to Local Using `sync`

Command:

```bash
aws s3 sync s3://my-devops-bucket/website/ ./website/
```

This synchronizes S3 objects to the local directory.

---

# 49. S3 to S3 Using `sync`

Command:

```bash
aws s3 sync s3://source-bucket/ s3://destination-bucket/
```

This synchronizes objects from one S3 location to another.

---

# 50. Difference Between `cp` and `sync`

| Feature                             | `cp`                    | `sync` |
| ----------------------------------- | ----------------------- | ------ |
| Copy files                          | Yes                     | Yes    |
| Copy folders                        | Yes, with `--recursive` | Yes    |
| Synchronization                     | No                      | Yes    |
| Recursive by default                | No                      | Yes    |
| Useful for one-time copy            | Yes                     | Yes    |
| Useful for repeated synchronization | Less suitable           | Yes    |
| Local → S3                          | Yes                     | Yes    |
| S3 → Local                          | Yes                     | Yes    |
| S3 → S3                             | Yes                     | Yes    |

---

# 51. `cp` vs `sync` Example

Suppose local directory contains:

```text
project/
├── file1.txt
├── file2.txt
└── file3.txt
```

Upload using:

```bash
aws s3 cp project/ s3://my-bucket/project/ --recursive
```

For repeated updates, use:

```bash
aws s3 sync project/ s3://my-bucket/project/
```

If a file has changed, `sync` can copy the changed file rather than blindly copying everything again.

---

# 52. Important `sync` Behavior

Suppose S3 contains:

```text
file1.txt
file2.txt
file3.txt
```

Local directory contains:

```text
file1.txt
file2.txt
```

Running:

```bash
aws s3 sync local/ s3://my-bucket/
```

does **not** normally delete `file3.txt` from S3 just because it is absent locally.

To make the destination mirror the source more closely, use:

```bash
aws s3 sync local/ s3://my-bucket/ --delete
```

### Warning

`--delete` can remove destination objects that are not present in the source.

Use it carefully.

---

# 53. `sync --delete` Example

Local:

```text
project/
├── app.jar
└── config.txt
```

S3:

```text
project/
├── app.jar
├── config.txt
└── old.txt
```

Command:

```bash
aws s3 sync project/ s3://my-bucket/project/ --delete
```

The destination can become:

```text
project/
├── app.jar
└── config.txt
```

`old.txt` can be deleted from the destination because it does not exist in the source.

---

# 54. Exclude Files

You can exclude files from the copy/sync operation.

Example:

```bash
aws s3 sync . s3://my-bucket/ --exclude "*.log"
```

This excludes `.log` files.

Example:

```bash
aws s3 sync . s3://my-bucket/ --exclude "*.tmp"
```

---

# 55. Include Specific Files

Example:

```bash
aws s3 sync . s3://my-bucket/ --exclude "*" --include "*.html"
```

This can be used to synchronize only HTML files.

---

# 56. Dry Run

Before executing a potentially destructive or large operation, use:

```bash
--dryrun
```

Example:

```bash
aws s3 sync . s3://my-bucket/ --dryrun
```

This shows what AWS CLI would do without actually performing the operation.

Useful for testing commands before execution.

---

# 57. Useful `cp` Options

### Recursive

```bash
--recursive
```

Used for folders.

Example:

```bash
aws s3 cp folder/ s3://my-bucket/folder/ --recursive
```

### Dry Run

```bash
--dryrun
```

### Exclude

```bash
--exclude "*.log"
```

### Include

```bash
--include "*.txt"
```

### Delete

```bash
--delete
```

Mostly used with `sync`.

---

# 58. S3 Upload Example – DevOps Project

Suppose Jenkins generates:

```text
target/application.war
```

Upload it to S3:

```bash
aws s3 cp target/application.war s3://my-devops-artifacts/
```

The artifact becomes:

```text
s3://my-devops-artifacts/application.war
```

---

# 59. Backup Example

Suppose database backup is:

```text
/home/ubuntu/backups/database.sql
```

Upload:

```bash
aws s3 cp /home/ubuntu/backups/database.sql s3://my-backup-bucket/database/
```

Result:

```text
s3://my-backup-bucket/database/database.sql
```

---

# 60. Backup Folder Using `sync`

Suppose:

```text
/home/ubuntu/backups/
├── db1.sql
├── db2.sql
└── db3.sql
```

Command:

```bash
aws s3 sync /home/ubuntu/backups/ s3://my-backup-bucket/database/
```

---

# 61. Download Backup from S3

```bash
aws s3 cp s3://my-backup-bucket/database/db1.sql /home/ubuntu/
```

---

# 62. Download Complete Backup Directory

```bash
aws s3 sync s3://my-backup-bucket/database/ /home/ubuntu/backups/
```

---

# 63. Useful S3 CLI Commands

### List all buckets

```bash
aws s3 ls
```

### List bucket contents

```bash
aws s3 ls s3://my-bucket/
```

### Recursive listing

```bash
aws s3 ls s3://my-bucket/ --recursive
```

### Create bucket

```bash
aws s3 mb s3://my-bucket --region ap-south-1
```

### Upload file

```bash
aws s3 cp file.txt s3://my-bucket/
```

### Upload directory

```bash
aws s3 cp folder/ s3://my-bucket/folder/ --recursive
```

### Download file

```bash
aws s3 cp s3://my-bucket/file.txt .
```

### Download directory

```bash
aws s3 cp s3://my-bucket/folder/ ./folder/ --recursive
```

### Sync local to S3

```bash
aws s3 sync folder/ s3://my-bucket/folder/
```

### Sync S3 to local

```bash
aws s3 sync s3://my-bucket/folder/ ./folder/
```

### Sync S3 to S3

```bash
aws s3 sync s3://source-bucket/ s3://destination-bucket/
```

### Delete an object

```bash
aws s3 rm s3://my-bucket/file.txt
```

### Delete all objects under a prefix

```bash
aws s3 rm s3://my-bucket/folder/ --recursive
```

### Remove an empty bucket

```bash
aws s3 rb s3://my-bucket
```

### Remove a bucket and its objects

```bash
aws s3 rb s3://my-bucket --force
```

> Use deletion commands carefully, especially in production.

---

# 64. S3 Access Flow

A simplified S3 authorization flow:

```text
User / Application
        |
        v
    AWS Request
        |
        v
   Authentication
        |
        v
   Authorization
        |
        +--------------------+
        |                    |
        v                    v
 IAM Policies          Bucket Policies
        |                    |
        +---------+----------+
                  |
                  v
             S3 Bucket
                  |
                  v
              Object
```

---

# 65. Important S3 Security Best Practices

1. Keep buckets private unless public access is required.

2. Enable Block Public Access.

3. Use IAM roles instead of hard-coded AWS credentials where possible.

4. Enable encryption.

5. Enable versioning for important data.

6. Use least-privilege IAM policies.

7. Use bucket policies carefully.

8. Enable logging/monitoring where required.

9. Use lifecycle policies to control storage costs.

10. Avoid using:

```json
"Principal": "*"
```

unless public access is intentionally required.

11. Protect sensitive data with appropriate encryption and access controls.

12. Regularly review bucket permissions.

---

# 66. Real-World DevOps Use Cases

## Use Case 1 – Application Artifacts

```text
Jenkins
   |
   | Build
   v
application.war
   |
   v
S3
```

Command:

```bash
aws s3 cp target/application.war s3://my-artifacts/
```

---

## Use Case 2 – Database Backup

```text
Database
   |
   v
backup.sql
   |
   v
S3
```

Command:

```bash
aws s3 cp backup.sql s3://my-backup-bucket/
```

Lifecycle:

```text
30 days  -> Standard-IA
90 days  -> Glacier
365 days -> Delete
```

---

## Use Case 3 – Static Website

```text
index.html
style.css
script.js
images/
```

can be stored in S3 for static website hosting.

---

## Use Case 4 – Log Storage

```text
Application
     |
     v
Logs
     |
     v
S3
     |
     +---- Lifecycle
             |
             +---- Glacier
```

---

# 67. Complete Practical Example

### Step 1 – Configure AWS CLI

```bash
aws configure
```

### Step 2 – Verify Identity

```bash
aws sts get-caller-identity
```

### Step 3 – Create Bucket

```bash
aws s3 mb s3://my-devops-training-bucket-2026 --region ap-south-1
```

### Step 4 – Verify Bucket

```bash
aws s3 ls
```

### Step 5 – Create Test File

```bash
echo "Hello S3" > test.txt
```

### Step 6 – Upload File

```bash
aws s3 cp test.txt s3://my-devops-training-bucket-2026/
```

### Step 7 – List Bucket

```bash
aws s3 ls s3://my-devops-training-bucket-2026/
```

### Step 8 – Download File

```bash
aws s3 cp s3://my-devops-training-bucket-2026/test.txt downloaded.txt
```

### Step 9 – Create Directory

```bash
mkdir project
```

Create files:

```bash
echo "Application" > project/app.txt
echo "Configuration" > project/config.txt
```

### Step 10 – Upload Directory

```bash
aws s3 cp project/ s3://my-devops-training-bucket-2026/project/ --recursive
```

### Step 11 – Synchronize Directory

```bash
aws s3 sync project/ s3://my-devops-training-bucket-2026/project/
```

### Step 12 – Download Using Sync

```bash
aws s3 sync s3://my-devops-training-bucket-2026/project/ ./downloaded-project/
```

---

# 68. Important Interview Questions

## Q1. What is Amazon S3?

Amazon S3 is an AWS object storage service used to store and retrieve data as objects inside buckets.

---

## Q2. What is an S3 bucket?

A bucket is a container used to store S3 objects.

---

## Q3. What is an S3 object?

An object is the actual data stored in S3 along with its key and metadata.

---

## Q4. What is an S3 object key?

The object key uniquely identifies an object within a bucket.

Example:

```text
backup/database.sql
```

---

## Q5. What is an S3 bucket policy?

A bucket policy is a resource-based JSON policy attached to an S3 bucket to control access.

---

## Q6. What is the difference between IAM policy and bucket policy?

IAM policies are identity-based policies attached to IAM identities, while bucket policies are resource-based policies attached to S3 buckets.

---

## Q7. What is S3 Lifecycle?

S3 Lifecycle automatically transitions or expires objects based on configured rules.

---

## Q8. Why do we use S3 Lifecycle?

To automatically move objects to cheaper storage classes or delete objects when they are no longer required.

---

## Q9. What is the difference between `aws s3 cp` and `aws s3 sync`?

`cp` copies files or directories, while `sync` synchronizes the source and destination and is useful for repeated synchronization.

---

## Q10. How do you upload a file to S3?

```bash
aws s3 cp file.txt s3://my-bucket/
```

---

## Q11. How do you upload a folder to S3?

```bash
aws s3 cp folder/ s3://my-bucket/folder/ --recursive
```

---

## Q12. How do you download a file from S3?

```bash
aws s3 cp s3://my-bucket/file.txt .
```

---

## Q13. How do you synchronize a local folder with S3?

```bash
aws s3 sync ./folder/ s3://my-bucket/folder/
```

---

## Q14. How do you synchronize S3 with a local folder?

```bash
aws s3 sync s3://my-bucket/folder/ ./folder/
```

---

## Q15. How do you copy data between two S3 buckets?

```bash
aws s3 cp s3://source-bucket/ s3://destination-bucket/ --recursive
```

or:

```bash
aws s3 sync s3://source-bucket/ s3://destination-bucket/
```

---

# 69. Quick Revision

```text
S3
 |
 +-- Bucket
 |     |
 |     +-- Objects
 |
 +-- Bucket Policy
 |
 +-- Versioning
 |
 +-- Encryption
 |
 +-- Lifecycle
 |
 +-- Storage Classes
 |
 +-- Block Public Access
 |
 +-- CLI
       |
       +-- aws s3 ls
       +-- aws s3 mb
       +-- aws s3 cp
       +-- aws s3 sync
       +-- aws s3 rm
       +-- aws s3 rb
```

### Most Important Commands

```bash
# Configure CLI
aws configure

# Check identity
aws sts get-caller-identity

# List buckets
aws s3 ls

# List bucket contents
aws s3 ls s3://my-bucket/

# Upload file
aws s3 cp file.txt s3://my-bucket/

# Download file
aws s3 cp s3://my-bucket/file.txt .

# Upload folder
aws s3 cp folder/ s3://my-bucket/folder/ --recursive

# Download folder
aws s3 cp s3://my-bucket/folder/ ./folder/ --recursive

# Sync local -> S3
aws s3 sync folder/ s3://my-bucket/folder/

# Sync S3 -> local
aws s3 sync s3://my-bucket/folder/ ./folder/

# Sync S3 -> S3
aws s3 sync s3://source-bucket/ s3://destination-bucket/

# Delete object
aws s3 rm s3://my-bucket/file.txt

# Recursive delete
aws s3 rm s3://my-bucket/folder/ --recursive

# Create bucket
aws s3 mb s3://my-bucket --region ap-south-1

# Remove empty bucket
aws s3 rb s3://my-bucket
```

---

# 70. Class 34 Summary

In this class we learned:

* What Amazon S3 is
* S3 buckets
* S3 objects
* Object keys
* S3 storage classes
* S3 versioning
* S3 encryption
* S3 bucket policies
* IAM policy vs bucket policy
* S3 Block Public Access
* S3 Lifecycle management
* Lifecycle transitions
* Lifecycle expiration
* AWS CLI configuration
* `aws s3 ls`
* `aws s3 mb`
* `aws s3 cp`
* `aws s3 sync`
* `aws s3 rm`
* `aws s3 rb`
* Uploading files to S3
* Downloading files from S3
* Copying data between S3 buckets
* Synchronizing local folders with S3
* `--recursive`
* `--exclude`
* `--include`
* `--dryrun`
* `--delete`
* S3 security best practices
* Real-world DevOps use cases
* S3 interview questions
