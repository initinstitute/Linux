# 📘 Class 15 Notes - Linux Users, Groups, Permissions, Sort, Ping & Curl

---

# 👥 Users in Linux

A **user** is an account that can log in and use the Linux system.

There are mainly two types of users:

- **Root User** – Has full administrative access.
- **Normal User** – Has limited permissions.

## View Current User

```bash
whoami
```

Example:

```bash
ubuntu
```

---

## View User ID Information

```bash
id
```

Example:

```bash
id ubuntu
```

Output:

```text
uid=1000(ubuntu) gid=1000(ubuntu) groups=1000(ubuntu)
```

Explanation:

- **uid** → User ID
- **gid** → Primary Group ID
- **groups** → Groups the user belongs to

---

## Display Current Logged-in Users

```bash
who
```

---

## Display All Users

```bash
cat /etc/passwd
```

Each line represents one user account.

---

# ➕ Creating Users

Only the root user or a user with **sudo** privileges can create users.

## Create a User

```bash
sudo useradd username
```

Example

```bash
sudo useradd devuser
```

---

## Create User with Home Directory

```bash
sudo useradd -m username
```

Example

```bash
sudo useradd -m harish
```

---

## Set Password

```bash
sudo passwd username
```

Example

```bash
sudo passwd harish
```

You will be prompted to enter and confirm the password.

---

## Switch User

```bash
su username
```

Example

```bash
su harish
```

---

## Delete User

```bash
sudo userdel username
```

Delete user along with home directory

```bash
sudo userdel -r username
```

---

# 👨‍👩‍👧 Groups in Linux

A **group** is a collection of users.

Groups help manage permissions easily.

Example:

- Developers
- Testers
- DevOps

Instead of giving permissions to each user, permissions can be assigned to a group.

---

## View Current User Groups

```bash
groups
```

Example

```bash
groups ubuntu
```

---

## Display All Groups

```bash
cat /etc/group
```

---

# ➕ Creating Groups

Create a new group

```bash
sudo groupadd developers
```

Verify

```bash
cat /etc/group
```

or

```bash
getent group developers
```

---

# ➕ Add User to a Group

Syntax

```bash
sudo usermod -aG groupname username
```

Example

```bash
sudo usermod -aG developers harish
```

Explanation

- **-a** → Append user to existing groups
- **-G** → Secondary group

---

## Verify User Groups

```bash
groups harish
```

or

```bash
id harish
```

---

## Remove User from Group

```bash
sudo gpasswd -d username groupname
```

Example

```bash
sudo gpasswd -d harish developers
```

---

# 📂 File Permissions

Linux controls who can:

- Read files
- Write files
- Execute files

Use

```bash
ls -l
```

Example

```text
-rwxr-xr--
```

---

## Understanding Permission Format

Example

```text
-rwxr-xr--
```

Breakdown

```text
- rwx r-x r--
```

| Position | Meaning |
|----------|----------|
| - | File type |
| rwx | Owner permissions |
| r-x | Group permissions |
| r-- | Others permissions |

---

# RWX Meaning

| Permission | Symbol | Value |
|------------|--------|-------|
| Read | r | 4 |
| Write | w | 2 |
| Execute | x | 1 |

---

## Permission Details

### Read (r)

Allows viewing file contents.

Example

```bash
cat file.txt
```

---

### Write (w)

Allows editing or deleting a file.

Example

```bash
echo "Hello" >> file.txt
```

---

### Execute (x)

Allows running the file as a program.

Example

```bash
./script.sh
```

---

# Numeric Permission Values

| Number | Permission |
|---------|------------|
| 7 | rwx |
| 6 | rw- |
| 5 | r-x |
| 4 | r-- |
| 3 | -wx |
| 2 | -w- |
| 1 | --x |
| 0 | --- |

---

# chmod Command

Used to change file permissions.

Syntax

```bash
chmod permissions filename
```

---

## Example 1

```bash
chmod 777 file.txt
```

Meaning

```text
Owner  : rwx
Group  : rwx
Others : rwx
```

Everyone has full access.

---

## Example 2

```bash
chmod 755 script.sh
```

Meaning

```text
Owner  : rwx
Group  : r-x
Others : r-x
```

Common for executable scripts.

---

## Example 3

```bash
chmod 644 file.txt
```

Meaning

```text
Owner  : rw-
Group  : r--
Others : r--
```

Common for text files.

---

## Using Symbolic Mode

Add execute permission

```bash
chmod +x script.sh
```

Remove write permission

```bash
chmod -w file.txt
```

Give owner execute permission

```bash
chmod u+x script.sh
```

Give group write permission

```bash
chmod g+w file.txt
```

Remove execute permission for others

```bash
chmod o-x script.sh
```

---

# 📊 sort Command

The **sort** command arranges the lines of a text file in alphabetical or numerical order.

---

## Syntax

```bash
sort filename
```

---

## Example

Create a file

```bash
cat > names.txt
```

Content

```text
John
Alice
David
Bob
```

Sort the file

```bash
sort names.txt
```

Output

```text
Alice
Bob
David
John
```

---

## Sort Numbers

```bash
sort -n numbers.txt
```

Example

Input

```text
50
8
100
25
```

Output

```text
8
25
50
100
```

---

## Reverse Sorting

```bash
sort -r names.txt
```

---

## Remove Duplicate Lines

```bash
sort -u names.txt
```

---

# 🌐 ping Command

The **ping** command checks whether another computer or website is reachable over the network. It also measures the response time.

---

## Syntax

```bash
ping hostname
```

Example

```bash
ping google.com
```

Output

```text
64 bytes from google.com:
```

Stop the command using

```text
Ctrl + C
```

---

## Send Limited Packets

```bash
ping -c 4 google.com
```

Sends only **4** packets.

---

## Uses of ping

- Check internet connectivity
- Verify a server is reachable
- Measure network latency
- Troubleshoot network issues

---

# 🌐 curl Command

The **curl** command is used to transfer data to or from a server. It is commonly used to test APIs, download files, and check websites.

---

## Check Website Response

```bash
curl https://example.com
```

Displays the HTML content of the webpage.

---

## View Only HTTP Headers

```bash
curl -I https://example.com
```

Example Output

```text
HTTP/2 200 OK
Content-Type: text/html
```

---

## Download a File

```bash
curl -O https://example.com/file.zip
```

---

## Save with a Different Name

```bash
curl -o myfile.zip https://example.com/file.zip
```

---

## Test a REST API

```bash
curl https://jsonplaceholder.typicode.com/posts/1
```

Returns JSON data.

---

# Difference Between ping and curl

| ping | curl |
|------|------|
| Checks network connectivity | Transfers data from a server |
| Uses ICMP protocol | Uses HTTP, HTTPS, FTP and many other protocols |
| Tests whether a host is reachable | Tests websites and APIs |
| Measures response time | Retrieves web pages, files, or API responses |
| Does not show webpage content | Shows webpage or API content |

---

# 📚 Summary

- **Users** are individual accounts that can log in to the system.
- **Groups** are collections of users used to manage permissions efficiently.
- Use **useradd** to create users and **groupadd** to create groups.
- Use **usermod -aG** to add a user to a group.
- **chmod** changes file permissions using numeric (755, 644, etc.) or symbolic modes (+x, -w).
- **r = 4**, **w = 2**, **x = 1** are used to calculate permission values.
- The **sort** command arranges text alphabetically or numerically.
- The **ping** command checks network connectivity and latency.
- The **curl** command communicates with web servers, APIs, and downloads files.
