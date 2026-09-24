# Class 48 - Kubernetes Restart Policy, Jobs and CronJobs
class url: https://youtu.be/p8Rqvd_RWWM
## Restart Policy

Restart Policy defines how Kubernetes should handle containers when they stop.

### 1. Always

* Default restart policy.
* Container is restarted whenever it stops.
* Even if the container exits successfully, Kubernetes restarts it.
* Commonly used with **Deployments**.

### 2. OnFailure

* Container is restarted only when it exits with an error.
* Commonly used with **Jobs**.
* If the container completes successfully, it will not be restarted.

### 3. Never

* Container is never restarted by the kubelet after it exits.
* Can be used with **Jobs**.

```yaml
restartPolicy: OnFailure
```

---

# Kubernetes Jobs

* A **Job** creates Pods to perform a task and ensures that the task completes successfully.
* Jobs are mainly used for **short-lived or batch workloads**.
* Once the required work is completed, the Job stops creating new Pods.

### Common Job Options

| Option                  | Description                                    |
| ----------------------- | ---------------------------------------------- |
| `completions`           | Number of successful completions required      |
| `parallelism`           | Number of Pods allowed to run at the same time |
| `activeDeadlineSeconds` | Maximum time allowed for the Job               |
| `backoffLimit`          | Number of retries allowed after failure        |

### completions

* Default value is `1`.
* Defines how many successful Pod completions are required.

```yaml
completions: 5
```

This means the Job needs **5 successful completions**.

### parallelism

* Default value is `1`.
* Defines how many Pods can run at the same time.

```yaml
parallelism: 2
```

This allows **2 Pods to run in parallel**.

### activeDeadlineSeconds

* Defines the maximum execution time for the Job.
* If the Job exceeds this time, Kubernetes terminates it.

```yaml
activeDeadlineSeconds: 100
```

The Job can run for a maximum of **100 seconds**.

### backoffLimit

* Defines how many times Kubernetes can retry a failed Job.
* Helps prevent continuous Pod creation when a Job keeps failing.

```yaml
backoffLimit: 3
```

The Job can retry up to **3 times** after failure.

---

# CronJob

* A **CronJob** creates Jobs at a scheduled time.
* It is useful for running tasks periodically.
* CronJobs use **cron syntax**.

```text
* * * * *
│ │ │ │ │
│ │ │ │ └── Day of week
│ │ │ └──── Month
│ │ └────── Day of month
│ └──────── Hour
└────────── Minute
```

### Example

```yaml
apiVersion: batch/v1
kind: CronJob

metadata:
  name: my-job

spec:
  schedule: "* * * * *"

  successfulJobsHistoryLimit: 0
  failedJobsHistoryLimit: 0

  jobTemplate:
    spec:
      template:
        metadata:
          name: my-busybox

        spec:
          containers:
          - name: busybox
            image: busybox
            command: ["sh", "-c", "sleep 2"]

          restartPolicy: OnFailure
```

### CronJob Example

```yaml
schedule: "* * * * *"
```

This runs the Job **every minute**.

---

# Job Use Cases

* **Automated Testing** – Run integration or end-to-end tests.
* **Database Maintenance** – Run backups, migrations, or cleanup.
* **Log Processing** – Process or analyze logs.
* **Data Processing** – Run ETL and batch-processing tasks.
* **File Processing** – Convert, compress, or process files.
* **Backup and Restore** – Automate backup and restore tasks.
* **Scheduled Tasks** – Run periodic maintenance using CronJobs.

## Job vs CronJob

| Job                         | CronJob                           |
| --------------------------- | --------------------------------- |
| Runs a task to completion   | Runs Jobs on a schedule           |
| Usually runs once           | Can run repeatedly                |
| No `schedule` field         | Uses `schedule`                   |
| Used for batch tasks        | Used for scheduled/periodic tasks |
| Example: database migration | Example: daily database backup    |
