# Linux Class Notes – Echo, Redirection and Grep

## 1. echo Command

The `echo` command is used to display a message or output a string to the terminal.

```bash
echo "Hello, World!"
```

### Output

```text
Hello, World!
```

---

# 2. Redirection Operator (>)

Redirection allows you to capture the output from a command and write it to a file.

### Key Points

* Overwrites the existing content of the file.
* If the file does not exist, it creates a new file.
* Output is written to the file and not displayed on the terminal.

### Redirect Text to a File

```bash
echo "This is Redirection of text to file" > file.txt
```

### Redirect Command Output to a File

```bash
ls -lrt > file.txt
```

### Empty a File Without Deleting It

```bash
> file.txt
```

---

# 3. Append Operator (>>)

Double redirection (`>>`) appends data to the end of a file.

### Key Points

* Preserves existing content.
* Adds new content at the end of the file.

### Append Text to a File

```bash
echo "This is appended text" >> file.txt
```

### Append Command Output to a File

```bash
ls -lrt >> file.txt
```

---

# 4. grep Command

`grep` stands for **Global Search for Regular Expression and Print**.

It searches for patterns in files and prints matching lines.

### Basic Search

```bash
grep "linux" file.txt
```

### Case-Insensitive Search

```bash
grep -i "linux" file.txt
```

### Match Whole Word Only

```bash
grep "the" file.txt
```

Matches:

```text
the
there
their
```

```bash
grep -w "the" file.txt
```

Matches only:

```text
the
```

---

# 5. Regular Expressions with grep

## 1. Lines Starting with "hello"

```bash
grep "^hello" file1
```

## 2. Lines Ending with "done"

```bash
grep "done$" file1
```

## 3. Lines Containing Any Character Between a-e

```bash
grep "[a-e]" file1
```

## 4. Lines Starting with a Vowel

```bash
grep "^[aeiou]" file1
```

## 5. Lines Starting with a Digit (after zero or more spaces)

```bash
grep " *[0-9]" file1
```

Examples:

```text
1. Linux
2. AWS
3. Docker
```

### Find Any 3-Digit Number

```bash
grep "[0-9][0-9][0-9]" file.txt
```

Examples:

```text
123
456
789
```

---

## 6. Match a 10-Digit Indian Mobile Number

```bash
grep -E "\b[6789][0-9]{9}\b" file.txt
```

Examples:

```text
9876543210
8123456789
```

---

# 6. Multiple Pattern Search

By default, grep searches for a single pattern.

Use `-e` to search for multiple patterns.

```bash
grep -e "linux" -e "aws" file.txt
```

---

# 7. Recursive Search

Search through files and directories recursively.

### Search for a Word in All Files

```bash
grep -r "linux" *
```

### Search for "error" in /var/log

```bash
grep -r "error" /var/log/
```

### Case-Insensitive Recursive Search

```bash
grep -ri "error" /var/log/
```

### Search Only in Python Files

```bash
grep -r "text" --include="*.py"
```

### Exclude Log Files

```bash
grep -r "text" --exclude="*.log"
```

---

# 8. Print Only Matching Content

Use `-o` to print only the matching portion.

### Extract Mobile Numbers

```bash
grep -o "[0-9]\{10\}" contacts.txt
```

Output:

```text
9876543210
8123456789
```

---

# 9. Show Line Numbers

Use `-n` to display line numbers along with matching lines.

```bash
grep -n "linux" file.txt
```

Example Output:

```text
5: Linux is an operating system
12: Linux supports multiple users
```

---

# 10. Extract IP Addresses

```bash
grep -E -w '([0-9]{1,3}\.){3}[0-9]{1,3}' file.txt
```

Example Matches:

```text
192.168.1.10
10.0.0.5
172.31.15.20
```

---

# 11. Extract Indian Mobile Numbers

```bash
grep -E -w '[6-9][0-9]{9}' file.txt
```

Example Matches:

```text
9876543210
8123456789
7012345678
```

---

# Quick Revision

| Command        | Purpose                          |
| -------------- | -------------------------------- |
| echo           | Display text on terminal         |
| >              | Overwrite file content           |
| >>             | Append content to file           |
| grep           | Search text patterns             |
| grep -i        | Ignore case                      |
| grep -w        | Match whole words                |
| grep -n        | Show line numbers                |
| grep -r        | Recursive search                 |
| grep -o        | Print only matched text          |
| grep -E        | Extended regular expressions     |
| grep --include | Search only specified file types |
| grep --exclude | Exclude specified file types     |



