# Class 58 – Auto Scaling Groups (ASG)
class url: https://youtu.be/_h3XfhbnzxI
## 1. What is Auto Scaling?

- AWS Auto Scaling automatically increases or decreases resources based on application demand.
- Amazon EC2 Auto Scaling maintains the required number of EC2 instances.
- It helps improve availability, performance, and cost efficiency.

## 2. Minimum, Desired, and Maximum Capacity

An Auto Scaling Group (ASG) has three important settings:

- **Minimum Capacity:** Minimum number of EC2 instances to maintain.
- **Desired Capacity:** Target number of EC2 instances.
- **Maximum Capacity:** Maximum number of EC2 instances allowed.

**Example:**

```text
Minimum Capacity = 1
Desired Capacity = 2
Maximum Capacity = 4
```

- Normally, ASG maintains 2 instances.
- If demand increases, it can scale up to 4 instances.
- If demand decreases, it can scale down to 1 instance.

## 3. Components of Auto Scaling

### 1. Auto Scaling Group

- Manages a group of EC2 instances as a single unit.
- Maintains the minimum, desired, and maximum capacity.
- Replaces unhealthy instances when health checks detect a failure.

### 2. Launch Template

A launch template defines how EC2 instances should be launched.

It includes:

- AMI ID
- Instance type
- Key pair
- Security groups
- EBS volume configuration
- User data script
- IAM instance profile

**Note:** Launch Templates are recommended over Launch Configurations because they provide more features.

### 3. Scaling Policies

Scaling policies determine when and how the number of EC2 instances changes.

Common types:

- **Dynamic Scaling:** Scales based on metrics such as CPU utilization.
- **Scheduled Scaling:** Scales at specific dates or times.
- **Predictive Scaling:** Uses forecasted demand to plan capacity in advance.

### 4. Lifecycle Hooks

- Lifecycle hooks allow you to perform actions when instances launch or terminate.
- They pause an instance's lifecycle transition while a task is completed.
- Example: Install software, register an instance, or perform cleanup before termination.
- The default wait period is 1 hour, and the timeout can be configured.

### 5. Instance Refresh

- Updates existing instances in an Auto Scaling Group.
- Useful when updating the AMI, launch template configuration, user data, or instance type.
- ASG gradually replaces existing instances according to the Instance Refresh configuration.
- Only one Instance Refresh can be active in an ASG at a time.

## 4. Types of Scaling Policies

| Policy | Description |
|---|---|
| Target Tracking | Maintains a target metric, such as average CPU utilization at 50%. |
| Step Scaling | Adds or removes capacity based on CloudWatch alarm thresholds and configured adjustment steps. |
| Simple Scaling | Scales based on a CloudWatch alarm and a scaling adjustment. |
| Scheduled Scaling | Changes capacity according to a schedule. |
| Predictive Scaling | Forecasts demand and schedules capacity in advance. |

## 5. Example Scenario

Assume an application is running on an ASG with:

```text
Minimum = 2
Desired = 2
Maximum = 10
CPU Target = 50%
```

- When CPU utilization increases, ASG launches additional instances.
- When CPU utilization decreases, ASG can terminate unnecessary instances.
- ASG never scales below 2 or above 10 instances under these settings.
- Actual scaling depends on the configured policy, metrics, and cooldown or instance warmup settings.

## 6. Health Checks

ASG can use:

- **EC2 Health Checks:** Check the health status of the EC2 instance.
- **Elastic Load Balancing Health Checks:** Check whether an instance is healthy according to the load balancer.

When an instance is detected as unhealthy, ASG can terminate it and launch a replacement.

## Summary

- **ASG:** Manages EC2 instances and maintains capacity.
- **Launch Template:** Defines the EC2 instance configuration.
- **Scaling Policy:** Controls when to scale up or down.
- **Lifecycle Hook:** Performs tasks during instance launch or termination.
- **Instance Refresh:** Replaces instances to apply configuration updates.
- **Health Checks:** Help ASG identify and replace unhealthy instances.
