# 📘 Class 20 Notes - Environment Variables (`export`, `unset`) and `crontab -e`
Recording Link: https://youtu.be/akCJtHGR6PM
## Objectives

In this class, we learned:

- What are Environment Variables?
- How to create Environment Variables using `export`
- How to view Environment Variables
- How to remove Environment Variables using `unset`
- Making Environment Variables permanent
- Introduction to Cron Jobs
- Scheduling tasks using `crontab -e`
- Understanding Cron syntax with examples

---

# 1. Environment Variables

## What are Environment Variables?

Environment Variables are variables that store information which can be used by the operating system, shell, or applications.

They help avoid hardcoding values like:

- File paths
- User names
- AWS Credentials
- Java Home
- Python Path
- Application configurations

For example:

Instead of writing:

```bash
/home/ubuntu/projects/myapp
```

You can store it in a variable:

```bash
PROJECT_HOME=/home/ubuntu/projects/myapp
```

and use:

```bash
$PROJECT_HOME
```

---

# 2. Viewing Existing Environment Variables

Use:

```bash
printenv
```

or

```bash
env
```

Example:

```bash
printenv
```

Output:

```
HOME=/home/ubuntu
USER=ubuntu
PATH=/usr/local/bin:/usr/bin:/bin
SHELL=/bin/bash
```

---

# 3. Creating an Environment Variable

Use:

```bash
export VARIABLE_NAME=value
```

Example:

```bash
export NAME=Harish
```

Check the value:

```bash
echo $NAME
```

Output:

```
Harish
```

---

## Another Example

```bash
export PROJECT=/home/ubuntu/project
```

Verify:

```bash
echo $PROJECT
```

Output:

```
/home/ubuntu/project
```

---

# 4. Using Environment Variables

Example:

```bash
mkdir $PROJECT
```

Instead of writing:

```bash
mkdir /home/ubuntu/project
```

Another example:

```bash
cd $PROJECT
```

This makes commands easier and reduces typing mistakes.

---

# 5. Viewing a Single Variable

Syntax:

```bash
echo $VARIABLE_NAME
```

Example:

```bash
echo $HOME
```

Output:

```
/home/ubuntu
```

Example:

```bash
echo $USER
```

Output:

```
ubuntu
```

---

# 6. Removing an Environment Variable

Use:

```bash
unset VARIABLE_NAME
```

Example:

```bash
unset NAME
```

Now check:

```bash
echo $NAME
```

Output:

```

```

The variable has been removed.

---

# 7. Temporary vs Permanent Variables

## Temporary Variable

Created using:

```bash
export JAVA_HOME=/opt/java
```

It exists only for the current terminal session.

If the terminal is closed, the variable is lost.

---

## Permanent Variable

Open:

```bash
nano ~/.bashrc
```

Add:

```bash
export JAVA_HOME=/opt/java
```

Save the file.

Reload:

```bash
source ~/.bashrc
```

Now the variable is available every time you log in.

---

# 8. Common Environment Variables

| Variable | Description |
|-----------|-------------|
| HOME | User's home directory |
| USER | Logged-in username |
| PATH | Locations of executable commands |
| SHELL | Current shell |
| PWD | Present working directory |
| HOSTNAME | System hostname |

---

# 9. Cron Job

## What is a Cron Job?

A Cron Job is used to schedule commands or scripts to run automatically at a specific date and time.

Examples:

- Daily backups
- Restart services
- Delete old logs
- Run shell scripts
- Database backups
- Send automated reports

---

# 10. Editing Cron Jobs

Open the cron editor:

```bash
crontab -e
```

The first time you run it, you may be asked to choose an editor.

Example:

```
Select an editor

1. nano
2. vim
3. ed
```

Choose:

```
1
```

---

# 11. Cron Syntax

```
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of Week (0-7)
│ │ │ └──── Month (1-12)
│ │ └────── Day of Month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)
```

---

# 12. Cron Field Explanation

| Field | Range |
|---------|--------|
| Minute | 0-59 |
| Hour | 0-23 |
| Day of Month | 1-31 |
| Month | 1-12 |
| Day of Week | 0-7 (Sunday = 0 or 7) |

---

# 13. Common Cron Examples

## Every minute

```bash
* * * * * date
```

---

## Every 5 minutes

```bash
*/5 * * * * date
```

---

## Every hour

```bash
0 * * * * date
```

---

## Every day at 10 PM

```bash
0 22 * * *
```

---

## Every day from 10:00 PM to 10:55 PM every 5 minutes

```bash
0,5,10,15,20,25,30,35,40,45,50,55 22 * * *
```

---

## Every Sunday at 8 AM

```bash
0 8 * * 0
```

---

## First day of every month

```bash
0 0 1 * *
```

---

# 14. Running a Shell Script Using Cron

Suppose you have:

```bash
/home/ubuntu/file.sh
```

Give execute permission:

```bash
chmod +x /home/ubuntu/file.sh
```

Edit cron:

```bash
crontab -e
```

Add:

```bash
*/5 * * * * /bin/bash /home/ubuntu/file.sh
```

The script runs every 5 minutes.

---

# 15. Viewing Existing Cron Jobs

```bash
crontab -l
```

This lists all scheduled cron jobs for the current user.

---

# 16. Removing Cron Jobs

Delete all cron jobs:

```bash
crontab -r
```

> **Warning:** This permanently removes all cron jobs for the current user.

---

# 17. Useful Special Characters in Cron

| Symbol | Meaning |
|---------|---------|
| * | Every value |
| , | Multiple values |
| - | Range of values |
| */5 | Every 5 units |

Examples:

```bash
1,15,30 * * * *
```

Runs at minutes 1, 15, and 30.

```bash
10-20 * * * *
```

Runs every minute from 10 to 20.

```bash
*/10 * * * *
```

Runs every 10 minutes.

---

# 18. Best Practices

- Use absolute paths for commands and scripts.
- Give execute permission to scripts.
- Test scripts manually before scheduling.
- Check cron entries using `crontab -l`.
- Store logs for debugging by redirecting output to a file.

Example:

```bash
*/5 * * * * /home/ubuntu/file.sh >> /home/ubuntu/cron.log 2>&1
```

This saves both standard output and errors to `cron.log`.

---

# Summary

In this class, we learned:

- What Environment Variables are
- Creating variables using `export`
- Viewing variables using `echo`, `env`, and `printenv`
- Removing variables using `unset`
- Making variables permanent using `~/.bashrc`
- Understanding Cron Jobs
- Editing cron schedules with `crontab -e`
- Cron syntax and field meanings
- Scheduling scripts to run automatically
- Viewing and deleting cron jobs
- Best practices for using cron
