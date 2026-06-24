# 📘 Class 14 Notes - Linux Commands

## 1. `wget` Command

### What is `wget`?

`wget` is a command-line utility used to download files from the internet using HTTP, HTTPS, or FTP protocols.

### Syntax

```bash
wget <URL>
```

### Example

```bash
wget https://example.com/file.zip
```

This command downloads `file.zip` from the given URL into the current directory.

### Download and Save with Different Name

```bash
wget -O myfile.zip https://example.com/file.zip
```

### Resume an Interrupted Download

```bash
wget -c https://example.com/file.zip
```

### Common Uses

- Download software packages
- Download configuration files
- Download backups
- Fetch files from web servers

---

## 2. `sudo` Command

### What is `sudo`?

`sudo` stands for **Super User DO**.

It allows a normal user to execute commands with administrative (root) privileges.

### Syntax

```bash
sudo <command>
```

### Example

```bash
sudo apt update
```

This command updates package information using root permissions.

### Why Use `sudo`?

Many system-level tasks require root privileges, such as:

- Installing software
- Removing software
- Managing services
- Editing system files

### Example

```bash
sudo apt install nginx
```

---

## 3. `systemctl` Command

### What is `systemctl`?

`systemctl` is used to manage services and the system state in Linux systems that use **systemd**.

### Stop a Service

```bash
sudo systemctl stop <service-name>
```

### Example

```bash
sudo systemctl stop nginx
```

This command stops the Nginx web server.

### Verify Service Status

```bash
sudo systemctl status nginx
```

### Other Useful Commands

#### Start a Service

```bash
sudo systemctl start nginx
```

#### Restart a Service

```bash
sudo systemctl restart nginx
```

#### Enable Service at Boot

```bash
sudo systemctl enable nginx
```

#### Disable Service at Boot

```bash
sudo systemctl disable nginx
```

---

## 4. `ps -ef` Command

### What is `ps`?

`ps` stands for **Process Status**.

It is used to display information about running processes.

### Syntax

```bash
ps -ef
```

### Example

```bash
ps -ef
```

### Sample Output

```bash
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 09:00 ?        00:00:02 /sbin/init
ubuntu    1234  1200  0 10:00 pts/0    00:00:00 bash
ubuntu    5678  1234  0 10:05 pts/0    00:00:00 java
```

### Meaning of Columns

| Column | Description |
|----------|-------------|
| UID | User running the process |
| PID | Process ID |
| PPID | Parent Process ID |
| C | CPU Usage |
| STIME | Start Time |
| TTY | Terminal |
| TIME | CPU Time Used |
| CMD | Command Name |

### Find a Specific Process

```bash
ps -ef | grep nginx
```

### Example Output

```bash
root      1456     1  0 09:10 ? 00:00:00 nginx
```

### Common Uses

- View running processes
- Find process IDs (PID)
- Troubleshoot applications
- Check service status

---

# Quick Summary

| Command | Purpose |
|----------|---------|
| `wget URL` | Download files from the internet |
| `sudo command` | Execute commands as root/admin |
| `sudo systemctl stop service` | Stop a running service |
| `ps -ef` | Display all running processes |
| `ps -ef \| grep process` | Search for a specific process |

---

## Practice Commands

```bash
# Download a file
wget https://example.com/test.zip

# Update packages
sudo apt update

# Stop Jenkins service
sudo systemctl stop jenkins

# View all processes
ps -ef

# Find Jenkins process
ps -ef | grep jenkins
```

# End of Class 14
