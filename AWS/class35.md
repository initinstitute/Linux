# Class 35 – Amazon CloudWatch, SNS & CloudTrail
class url: https://youtu.be/bGZ8HRe5_pI
## 1. Amazon CloudWatch

### What is Amazon CloudWatch?

**Amazon CloudWatch** is an AWS monitoring and observability service used to monitor AWS resources, applications, and infrastructure.

CloudWatch can collect:

* Metrics
* Logs
* Events
* Alarms
* Application performance information

### Common AWS Services Monitored by CloudWatch

* EC2
* EBS
* S3
* RDS
* Lambda
* Load Balancer
* VPC
* ECS
* DynamoDB

---

## 2. CloudWatch Important Components

### Metrics

Metrics are numerical measurements collected from AWS resources.

For an EC2 instance, common metrics include:

* CPUUtilization
* NetworkIn
* NetworkOut
* DiskReadOps
* DiskWriteOps
* StatusCheckFailed

Example:

```text
CPUUtilization = 75%
```

This means the EC2 instance is currently using approximately 75% CPU.

---

### Logs

CloudWatch Logs can collect application and system logs.

Example:

```text
Application Logs
      |
      v
CloudWatch Logs
      |
      v
Monitoring / Troubleshooting
```

CloudWatch Agent can be installed on EC2 to send operating-system-level logs and metrics to CloudWatch.

---

### Alarms

A CloudWatch Alarm monitors a metric and performs an action when a configured threshold is reached.

Example:

```text
EC2 CPU Utilization
        |
        v
    CloudWatch
        |
        v
 CPU > 80% for 5 minutes
        |
        v
     Alarm
        |
        v
      SNS
        |
        v
 Email Notification
```

---

# 3. Creating a CloudWatch Alarm for EC2

### Step 1: Launch an EC2 Instance

Create or use an existing EC2 instance.

Make sure the instance is in the required AWS Region.

Example:

```text
Region: ap-south-1
```

### Step 2: Open CloudWatch

Go to:

```text
AWS Console
   → CloudWatch
   → Alarms
   → All alarms
   → Create alarm
```

### Step 3: Select Metric

Choose:

```text
Select metric
→ EC2
→ Per-Instance Metrics
→ CPUUtilization
```

Select the required EC2 instance.

### Step 4: Configure Condition

Example:

```text
Metric: CPUUtilization
Statistic: Average
Period: 5 minutes
Threshold: Greater than 80%
```

Meaning:

```text
If CPU > 80%
for the configured evaluation period
        ↓
CloudWatch Alarm changes state
```

### Step 5: Configure Notification

Under alarm notification/action:

```text
Alarm state trigger:
In alarm

Notification:
Send notification to SNS topic
```

Select an existing SNS topic or create a new one.

### Step 6: Give Alarm Name

Example:

```text
EC2-High-CPU-Alarm
```

Click:

```text
Create alarm
```

---

# 4. Amazon SNS

## What is SNS?

**Amazon Simple Notification Service (SNS)** is a messaging service used to send notifications and messages to subscribers.

SNS can be used with:

* CloudWatch
* Lambda
* S3
* Application services
* Other AWS services

---

## SNS Architecture

```text
CloudWatch Alarm
       |
       v
   SNS Topic
       |
       +--------> Email
       |
       +--------> SMS
       |
       +--------> Lambda
       |
       +--------> SQS
       |
       +--------> HTTP/HTTPS
```

---

# 5. Creating an SNS Topic

### Step 1: Open SNS

Go to:

```text
AWS Console
→ SNS
→ Topics
→ Create topic
```

### Step 2: Select Topic Type

For a basic notification setup:

```text
Type: Standard
```

### Step 3: Enter Topic Name

Example:

```text
cloudwatch-alerts
```

Click:

```text
Create topic
```

---

# 6. Create an SNS Subscription

After creating the topic:

```text
SNS
→ Topics
→ cloudwatch-alerts
→ Create subscription
```

Select:

```text
Protocol: Email
Endpoint: your-email@example.com
```

Click:

```text
Create subscription
```

An email confirmation will be sent.

Open the email and click:

```text
Confirm subscription
```

The subscription status should become:

```text
Confirmed
```

---

# 7. Connect CloudWatch with SNS

The complete flow is:

```text
EC2
 |
 | CPU Metric
 v
CloudWatch
 |
 | CPU > 80%
 v
CloudWatch Alarm
 |
 v
SNS Topic
 |
 v
Email Notification
```

### Configuration

While creating or editing the CloudWatch alarm:

```text
Alarm condition:
CPUUtilization > 80%

Alarm action:
Send notification to SNS

SNS Topic:
cloudwatch-alerts
```

When the alarm enters the `ALARM` state, SNS sends a notification to the subscribed email address.

---

# 8. Testing CloudWatch + SNS

To test the alarm:

### Option 1: Generate CPU Load

Connect to the EC2 instance:

```bash
ssh -i key.pem ubuntu@<PUBLIC-IP>
```

Install stress if required:

```bash
sudo apt update
sudo apt install stress -y
```

Generate CPU load:

```bash
stress --cpu 2 --timeout 300
```

Check CloudWatch.

The CPU utilization should increase.

If the configured threshold is crossed:

```text
CPU > 80%
    ↓
CloudWatch Alarm
    ↓
ALARM state
    ↓
SNS
    ↓
Email
```

---

# 9. CloudWatch Alarm States

A CloudWatch alarm normally has three states:

### OK

The metric is within the configured threshold.

```text
CPU < 80%
```

### ALARM

The metric has crossed the configured threshold.

```text
CPU > 80%
```

### INSUFFICIENT_DATA

CloudWatch does not have enough data to determine the alarm state.

```text
Insufficient monitoring data
```

---

# 10. Amazon CloudTrail

## What is CloudTrail?

**AWS CloudTrail** is a service used to record and track API activity and actions performed in an AWS account.

CloudTrail helps answer:

```text
Who performed the action?
What action was performed?
When was it performed?
Which AWS resource was affected?
From where was the request made?
```

---

# 11. Example CloudTrail Event

Suppose someone terminates an EC2 instance.

CloudTrail can record information such as:

```text
Event Name:
TerminateInstances

User:
admin-user

Time:
2026-08-12 20:30:00

Resource:
EC2 Instance

Source IP:
x.x.x.x
```

This is useful for auditing and troubleshooting.

---

# 12. CloudTrail vs CloudWatch

| Feature             | CloudWatch    | CloudTrail   |
| ------------------- | ------------- | ------------ |
| Main purpose        | Monitoring    | Auditing     |
| Metrics             | Yes           | No           |
| Logs                | Yes           | API activity |
| Alarms              | Yes           | No           |
| Tracks API calls    | Not primarily | Yes          |
| Tracks user actions | Limited       | Yes          |
| SNS integration     | Yes           | Yes          |
| Troubleshooting     | Yes           | Yes          |
| Security auditing   | Yes           | Very useful  |

### Simple Difference

```text
CloudWatch
    ↓
"What is happening?"

CloudTrail
    ↓
"Who did what?"
```

---

# 13. Creating a CloudTrail Trail

### Step 1: Open CloudTrail

Go to:

```text
AWS Console
→ CloudTrail
→ Trails
→ Create trail
```

### Step 2: Enter Trail Name

Example:

```text
my-management-events-trail
```

### Step 3: Configure Event Types

For basic auditing, enable:

```text
Management events
```

Management events record control-plane operations such as:

```text
CreateBucket
RunInstances
TerminateInstances
CreateUser
DeleteUser
CreateSecurityGroup
```

### Step 4: Configure S3 Bucket

CloudTrail can store logs in an S3 bucket.

Example:

```text
S3 Bucket:
my-cloudtrail-logs
```

CloudTrail delivers log files to the configured S3 bucket.

### Step 5: Create Trail

Click:

```text
Create trail
```

CloudTrail will start recording the configured events.

---

# 14. Viewing CloudTrail Events

Go to:

```text
CloudTrail
→ Event history
```

You can search events using:

* Event name
* Username
* Resource
* Event source
* Date/time
* AWS Region

Example:

```text
Event name:
RunInstances
```

or:

```text
Event name:
TerminateInstances
```

---

# 15. CloudTrail Event Flow

```text
User / IAM Role
       |
       | AWS API Request
       v
     AWS Service
       |
       v
   CloudTrail
       |
       v
   Event History
       |
       v
   S3 / CloudWatch Logs
```

---

# 16. CloudTrail + CloudWatch Logs

CloudTrail can also send events to a CloudWatch Logs log group.

Example:

```text
AWS API Activity
       |
       v
   CloudTrail
       |
       v
CloudWatch Logs
       |
       v
Monitoring / Analysis
```

This allows CloudWatch to be used for monitoring CloudTrail events.

---

# 17. CloudTrail + SNS

CloudTrail can also be integrated into notification workflows.

Example:

```text
IAM / EC2 / S3 API Activity
          |
          v
      CloudTrail
          |
          v
   CloudWatch Logs
          |
          v
 CloudWatch Metric Filter
          |
          v
   CloudWatch Alarm
          |
          v
         SNS
          |
          v
       Email
```

Example use case:

```text
Someone deletes an S3 bucket
        ↓
CloudTrail records DeleteBucket
        ↓
CloudWatch detects the event
        ↓
Alarm is triggered
        ↓
SNS sends notification
```

---

# 18. Important CloudWatch + SNS + CloudTrail Use Case

A common DevOps monitoring architecture:

```text
                 AWS ACCOUNT
                     |
       +-------------+-------------+
       |             |             |
      EC2           S3            IAM
       |             |             |
       +-------------+-------------+
                     |
                CloudTrail
                     |
                     v
              Audit / API Logs
                     |
                     v
              CloudWatch Logs
                     |
                     v
              Metric / Alarm
                     |
                     v
                   SNS
                     |
             +-------+-------+
             |               |
           Email           Lambda
```

At the same time:

```text
EC2 Metrics
     |
     v
CloudWatch
     |
     v
CPU / Memory / Network
     |
     v
CloudWatch Alarm
     |
     v
SNS
     |
     v
Email
```

---

# 19. Important Terms

### CloudWatch

Used for:

```text
Monitoring
Metrics
Logs
Alarms
Dashboards
```

### SNS

Used for:

```text
Notifications
Pub/Sub messaging
Email
SMS
SQS
Lambda
HTTP/HTTPS
```

### CloudTrail

Used for:

```text
Auditing
API activity
User activity
AWS account activity
Security investigation
```

---

# 20. Interview Questions

### Q1. What is CloudWatch?

CloudWatch is an AWS monitoring and observability service used to collect metrics, logs, monitor resources, and create alarms.

### Q2. What is SNS?

SNS is a managed messaging service used to send notifications to subscribers.

### Q3. What is CloudTrail?

CloudTrail records AWS API activity and provides auditing information about actions performed in an AWS account.

### Q4. Difference between CloudWatch and CloudTrail?

```text
CloudWatch → Monitoring
CloudTrail  → Auditing
```

### Q5. How do you send a CloudWatch alarm notification?

```text
CloudWatch Alarm
      ↓
SNS Topic
      ↓
SNS Subscription
      ↓
Email/SMS/etc.
```

### Q6. What happens when an EC2 CPU exceeds the alarm threshold?

```text
EC2 CPU Metric
      ↓
CloudWatch
      ↓
Alarm enters ALARM state
      ↓
SNS Topic
      ↓
Subscriber receives notification
```

### Q7. Where can CloudTrail logs be stored?

CloudTrail events can be viewed in Event history and trails can deliver logs to destinations such as an S3 bucket and CloudWatch Logs.

---

# 21. Practical Class Exercise

## Exercise 1 – CloudWatch

Create:

```text
EC2 Instance
     ↓
CPUUtilization Metric
     ↓
CloudWatch Alarm
```

Configure:

```text
Threshold: CPU > 80%
Period: 5 minutes
```

---

## Exercise 2 – SNS

Create:

```text
SNS Topic:
cloudwatch-alerts
```

Add:

```text
Email Subscription
```

Confirm the subscription from email.

---

## Exercise 3 – Connect CloudWatch to SNS

Configure:

```text
CloudWatch Alarm
       ↓
SNS Topic
       ↓
Email
```

Generate CPU load and verify the notification.

---

## Exercise 4 – CloudTrail

Create:

```text
CloudTrail Trail
       ↓
Management Events
       ↓
S3 Bucket
```

Perform an AWS action such as:

```text
Start EC2
Stop EC2
Create Security Group
Create S3 Bucket
```

Then check:

```text
CloudTrail
→ Event history
```

Identify:

```text
User
Event Name
Event Time
AWS Service
Source IP
Resource
```

---

# 22. Final Summary

```text
CloudWatch
    ↓
Monitor AWS resources
    ↓
Metrics / Logs / Alarms
    ↓
SNS
    ↓
Notifications

CloudTrail
    ↓
Record AWS API activity
    ↓
Audit who did what
    ↓
S3 / CloudWatch Logs
```

### Remember

```text
CloudWatch = Monitoring

SNS = Notification

CloudTrail = Auditing
```
