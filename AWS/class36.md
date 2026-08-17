# Class 36 — AWS Lambda
class url: https://youtu.be/KDRnq0GEWN4
## 1. What is AWS Lambda?

**AWS Lambda** is a serverless compute service provided by AWS.

It allows us to run code without creating or managing servers.

### Benefits

* No server management
* No need to create EC2 for running scripts
* Automatically executes code
* Supports multiple programming languages
* Can be triggered manually or automatically
* Useful for AWS automation

---

# 2. Common Lambda Use Cases

* Start EC2 instances
* Stop EC2 instances
* Create EBS snapshots
* Process S3 files
* Send SNS notifications
* Automate AWS resources
* Run scheduled tasks
* Process CloudWatch events

---

# 3. Lambda Architecture

```text
Event / User
     |
     v
  Lambda
     |
     v
AWS Service
```

Example:

```text
EventBridge
     |
     v
Lambda
     |
     v
EC2
```

---

# 4. Create Lambda Function

Go to:

```text
AWS Console
   ↓
Lambda
   ↓
Functions
   ↓
Create function
```

Select:

```text
Author from scratch
```

Example:

```text
Function name: stop-all-ec2

Runtime: Python 3.x
```

Create the function.

---

# 5. Lambda Execution Role

Lambda needs permission to access AWS services.

For stopping EC2 instances, Lambda needs:

```text
ec2:DescribeInstances
ec2:StopInstances
```

Example IAM policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances",
        "ec2:StopInstances"
      ],
      "Resource": "*"
    }
  ]
}
```

Attach this policy to the Lambda execution role.

---

# 6. What is Boto3?

**Boto3** is the AWS SDK for Python.

It allows Python code to communicate with AWS services.

Example:

```python
import boto3
```

Create an EC2 client:

```python
ec2 = boto3.client("ec2")
```

---

# 7. Lambda Handler

The main function executed by Lambda is called the handler.

Example:

```python
def lambda_handler(event, context):

    print("Lambda function started")
```

The standard Python Lambda handler is:

```text
lambda_function.lambda_handler
```

---

# 8. Example — Stop All Running EC2 Instances in ap-south-1

In this example, Lambda will:

1. Connect to EC2 in `ap-south-1`
2. Find all running EC2 instances
3. Get their instance IDs
4. Stop all of them

### Lambda Code

```python
import boto3


def lambda_handler(event, context):

    # Connect to EC2 in Mumbai region
    ec2 = boto3.client(
        "ec2",
        region_name="ap-south-1"
    )

    # Find all running EC2 instances
    response = ec2.describe_instances(
        Filters=[
            {
                "Name": "instance-state-name",
                "Values": ["running"]
            }
        ]
    )

    instance_ids = []

    # Get instance IDs
    for reservation in response["Reservations"]:
        for instance in reservation["Instances"]:
            instance_ids.append(
                instance["InstanceId"]
            )

    # Stop instances
    if instance_ids:

        ec2.stop_instances(
            InstanceIds=instance_ids
        )

        print("Stopped EC2 instances:")
        print(instance_ids)

    else:

        print("No running EC2 instances found")

    return {
        "statusCode": 200,
        "body": "EC2 stop operation completed"
    }
```

---

# 9. Code Explanation

### Connect to ap-south-1

```python
ec2 = boto3.client(
    "ec2",
    region_name="ap-south-1"
)
```

`ap-south-1` is the AWS Mumbai region.

---

### Find Running Instances

```python
response = ec2.describe_instances(
    Filters=[
        {
            "Name": "instance-state-name",
            "Values": ["running"]
        }
    ]
)
```

This returns only EC2 instances whose state is:

```text
running
```

---

### Get Instance IDs

```python
instance_ids.append(
    instance["InstanceId"]
)
```

Example:

```text
i-0123456789abcdef0
i-0abcdef123456789
```

---

### Stop Instances

```python
ec2.stop_instances(
    InstanceIds=instance_ids
)
```

This stops all the instance IDs collected by the function.

---

# 10. Lambda Execution Flow

```text
Lambda Function
      |
      v
Connect to ap-south-1
      |
      v
Find Running EC2 Instances
      |
      v
Get Instance IDs
      |
      v
Stop EC2 Instances
      |
      v
Print Result
```

---

# 11. Test the Lambda

Go to:

```text
Lambda
   ↓
stop-all-ec2
   ↓
Test
```

Create a test event:

```json
{}
```

Click:

```text
Test
```

Lambda will execute the Python function.

---

# 12. Check Result

After execution, check:

```text
Execution result
```

You can also check logs in:

```text
CloudWatch
   ↓
Logs
   ↓
Log groups
   ↓
/aws/lambda/stop-all-ec2
```

Example output:

```text
Stopped EC2 instances:
['i-0123456789abcdef0', 'i-0abcdef123456789']
```

---

# 13. Automatically Run Lambda

We can use **Amazon EventBridge** to run the Lambda automatically.

Example:

```text
EventBridge
     |
     | Every day at 11 PM
     v
Lambda
     |
     v
Stop EC2 Instances
```

This can be used to automatically stop development EC2 instances after working hours.

---

# 14. Important Note

The above function stops **all running EC2 instances in `ap-south-1`** that the Lambda role is allowed to stop.

For production environments, it is safer to stop only specific instances using tags.

Example tag:

```text
Environment = Dev
```

Then the Lambda can stop only development instances.

---

# 15. Important Points

| Topic                  | Description                   |
| ---------------------- | ----------------------------- |
| Lambda                 | Serverless compute service    |
| Runtime                | Python                        |
| Boto3                  | AWS SDK for Python            |
| IAM Role               | Gives Lambda AWS permissions  |
| `describe_instances()` | Gets EC2 instances            |
| `stop_instances()`     | Stops EC2 instances           |
| `ap-south-1`           | Mumbai AWS Region             |
| CloudWatch             | Stores Lambda logs            |
| EventBridge            | Can schedule Lambda execution |

---

# 16. Class 36 Summary

```text
AWS Lambda
    |
    +-- Serverless service
    |
    +-- Run Python code
    |
    +-- Uses IAM Execution Role
    |
    +-- Uses Boto3
    |
    +-- Can manage EC2
    |
    +-- Can be scheduled using EventBridge
```

### Practical

```text
Create Lambda
      ↓
Create IAM Role
      ↓
Add EC2 Permissions
      ↓
Write Python Code
      ↓
Connect to ap-south-1
      ↓
Find Running EC2 Instances
      ↓
Stop EC2 Instances
      ↓
Check CloudWatch Logs
```
