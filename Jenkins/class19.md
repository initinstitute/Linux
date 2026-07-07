# 📘 Jenkins Security and Cron Jobs

# 1. Jenkins Security Using Matrix-Based Security

## What is Jenkins Security?

Jenkins Security is used to protect the Jenkins server from unauthorized users. It ensures that only authenticated users can access Jenkins and perform actions based on the permissions assigned to them.

Without security:
- Anyone can access Jenkins.
- Anyone can modify or delete jobs.
- Anyone can configure the Jenkins server.
- Sensitive credentials may be exposed.

Therefore, security should always be enabled.

---

# Authentication vs Authorization

## Authentication

Authentication verifies **who the user is**.

Example:

```
Username: developer
Password: ********
```

If the username and password are correct, Jenkins allows the user to log in.

---

## Authorization

Authorization determines **what the user is allowed to do** after logging in.

Examples:
- Can the user create jobs?
- Can the user trigger builds?
- Can the user delete jobs?
- Can the user manage Jenkins?

---

# Creating Users in Jenkins

## Step 1: Enable Jenkins Security

Navigate to:

```
Dashboard
    ↓
Manage Jenkins
    ↓
Security
```

Enable:

```
✔ Enable Security
```

For **Security Realm**, select:

```
Jenkins' own user database
```

Enable:

```
✔ Allow users to sign up
```

(Optional – Disable this option after creating all required users.)

Click **Save**.

---

## Step 2: Create an Admin User

Navigate to:

```
Dashboard
    ↓
Manage Jenkins
    ↓
Manage Users
```

Click:

```
Create User
```

Fill in the following details:

```
Username
Password
Confirm Password
Full Name
Email Address
```

Click:

```
Create User
```

Example:

| Username | Role |
|----------|------|
| admin | Jenkins Administrator |

---

## Step 3: Create Additional Users

Repeat the same steps to create users.

Example:

| Username | Purpose |
|----------|---------|
| developer | Create and build jobs |
| tester | Trigger builds and view results |
| intern | Read-only access |

---

# Matrix-Based Security

## What is Matrix-Based Security?

Matrix-Based Security allows administrators to assign **specific permissions** to each user or group.

Each permission can be granted or denied independently.

Think of it like an Excel sheet:

- Rows = Users
- Columns = Permissions

---

# Enable Matrix-Based Security

Navigate to:

```
Dashboard
    ↓
Manage Jenkins
    ↓
Security
```

Under **Authorization**, select:

```
Matrix-based security
```

Click **Add User** and enter the usernames you created.

Example:

```
admin
developer
tester
intern
```

---

# Common Permissions

| Permission | Description |
|------------|-------------|
| Overall → Read | View Jenkins |
| Overall → Administer | Full administrator access |
| Job → Read | View jobs |
| Job → Build | Run builds |
| Job → Configure | Modify jobs |
| Job → Create | Create new jobs |
| Job → Delete | Delete jobs |
| Job → Cancel | Stop running builds |
| Credentials → View | View credentials |
| Credentials → Create | Add credentials |

---

# Example Permission Matrix

| Permission | admin | developer | tester | intern |
|------------|:-----:|:---------:|:------:|:------:|
| Overall Read | ✅ | ✅ | ✅ | ✅ |
| Overall Administer | ✅ | ❌ | ❌ | ❌ |
| Job Read | ✅ | ✅ | ✅ | ✅ |
| Job Build | ✅ | ✅ | ✅ | ❌ |
| Job Configure | ✅ | ✅ | ❌ | ❌ |
| Job Create | ✅ | ✅ | ❌ | ❌ |
| Job Delete | ✅ | ❌ | ❌ | ❌ |
| Job Cancel | ✅ | ✅ | ✅ | ❌ |

---

# Result

### Admin
- Full control over Jenkins.

### Developer
- Can create, configure, and build jobs.
- Cannot delete jobs or manage Jenkins.

### Tester
- Can view jobs and trigger builds.
- Cannot modify job configurations.

### Intern
- Can only view jobs.

---

# Advantages of Matrix-Based Security

- Protects Jenkins from unauthorized changes.
- Assigns permissions based on user roles.
- Prevents accidental deletion of jobs.
- Improves security in multi-user environments.
- Easy to manage users and permissions.

---

# 2. Jenkins Cron Jobs

## What is a Cron Job?

A **Cron Job** is a scheduled task that runs automatically at a specified time or interval.

In Jenkins, cron jobs are used to trigger builds automatically without manually clicking **Build Now**.

---

# Why Use Cron Jobs?

Cron jobs are commonly used for:

- Nightly builds
- Automated testing
- Daily backups
- Weekly reports
- Log cleanup
- Scheduled deployments

---

# Configuring a Cron Job

Open the Jenkins job and navigate to:

```
Dashboard
    ↓
Select Job
    ↓
Configure
    ↓
Build Triggers
    ↓
✔ Build periodically
```

Enter a cron expression in the schedule box.

---

# Cron Expression Format

A Jenkins cron expression consists of five fields:

```text
MINUTE   HOUR   DAY_OF_MONTH   MONTH   DAY_OF_WEEK
```

| Field | Allowed Values |
|--------|----------------|
| Minute | 0–59 |
| Hour | 0–23 |
| Day of Month | 1–31 |
| Month | 1–12 or JAN–DEC |
| Day of Week | 0–7 (0 or 7 = Sunday) |

---

# Special Characters

## `*` (Asterisk)

Represents **every value**.

Example:

```text
* * * * *
```

Runs every minute.

---

## `*/5` (Step Value)

Runs every five units.

Example:

```text
*/5 * * * *
```

Runs every 5 minutes:

```text
00
05
10
15
20
25
30
35
40
45
50
55
```

---

## `H` (Hash)

`H` is a Jenkins-specific feature.

Instead of running all jobs at the same minute, Jenkins calculates a stable minute based on the job name.

Example:

```text
H * * * *
```

Job A may run at:

```text
17 minutes past every hour
```

Job B may run at:

```text
42 minutes past every hour
```

This helps distribute the workload across the Jenkins server.

---

## `H/5`

Runs every five minutes starting from a Jenkins-calculated minute.

Example:

```text
H/5 * * * *
```

Possible execution times:

```text
02
07
12
17
22
27
32
37
42
47
52
57
```

Another job may have a different schedule, reducing simultaneous builds.

---

# Common Cron Examples

| Cron Expression | Description |
|-----------------|-------------|
| `* * * * *` | Every minute |
| `*/5 * * * *` | Every 5 minutes |
| `H/5 * * * *` | Every 5 minutes using Jenkins hash |
| `0 * * * *` | Every hour |
| `0 10 * * *` | Every day at 10:00 AM |
| `0 22 * * *` | Every day at 10:00 PM |
| `*/5 22 * * *` | Every 5 minutes between 10:00 PM and 10:55 PM |
| `0 0 * * 0` | Every Sunday at midnight |
| `0 0 1 * *` | First day of every month |

---

# Difference Between `*/5` and `H/5`

| `*/5` | `H/5` |
|--------|--------|
| Starts at minute 0 | Starts at a Jenkins-calculated minute |
| All jobs run at the same time | Jobs are distributed across different times |
| May increase server load | Reduces server load |
| Standard cron syntax | Jenkins-specific syntax |

---

# Best Practices

- Always enable Jenkins security before creating users.
- Follow the **Principle of Least Privilege** by granting only the permissions users need.
- Create separate accounts for administrators, developers, testers, and viewers.
- Use **Matrix-Based Security** for fine-grained permission control.
- Prefer **`H`** or **`H/5`** in Jenkins cron schedules to distribute builds evenly and avoid overloading the server.
- Test cron expressions before using them in production.

---

# Summary

In this class, you learned how to secure a Jenkins server by enabling security,
creating users, and assigning permissions using **Matrix-Based Security**.
You also learned how Jenkins **Cron Jobs** automate builds using cron expressions, 
the meaning of cron fields and special characters (`*`, `*/5`, `H`, and `H/5`), 
and how to schedule jobs efficiently while balancing server load.
