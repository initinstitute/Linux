# Class 6 Notes - Linux Command Operators and Find Command

## Semicolon (;)

The semicolon (`;`) operator executes commands sequentially, regardless of the success or failure of preceding commands.

### Syntax
```bash
<COMMAND1>; <COMMAND2>; <COMMANDN>
```

### Example
```bash
pwd; date; whoami
```

---

## && - Logical AND

The `&&` operator allows you to execute multiple commands in sequence.

The next command executes only if the previous command is successful (returns exit status `0`).

### Syntax
```bash
command_1 && command_2
```

### Examples

#### Create Directory and Move into It
```bash
mkdir new_folder && cd new_folder
```

#### Update and Install Packages
```bash
sudo apt update && sudo apt install <package_name>
```

---

## || - Logical OR

The `||` operator executes the next command only if the previous command fails.

### Syntax
```bash
command_1 || command_2
```

### Example
```bash
cd mydir || mkdir mydir
```

---

# xargs

`xargs` reads data from standard input and converts it into arguments for a specified command.

It is useful when a command does not accept standard input directly.

### Key Points

- Converts multiline input into a single-line, space-separated argument list.
- If no command is specified, `xargs` defaults to `echo`.
- The `-I` option allows replacement of a specified string with input values.

### Syntax
```bash
xargs -I <replace-string> <command> <command-arguments>
```

### Example
```bash
xargs -I{} rm {}
```

---

# Pipe (|)

The pipe (`|`) sends the output of one command as input to another command.

### Syntax
```bash
command_1 | command_2 | command_3 | ... | command_N
```

### Example
```bash
cat file.txt | grep error
```

---

# Real-World Examples Using Pipe (|) and xargs

## 1. Count Number of Files in a Directory

```bash
find . -type f | wc -l
```

## 2. Find and Count Specific Files

```bash
find . -type f -name "*.txt" | wc -l
```

## 3. Remove Files with a Specific Extension

```bash
find /path/to/directory -type f -name "*.log" | xargs rm
```

## 4. Check Whether a Process Is Running

```bash
ps -ef | grep <process_name>
```

## 5. Rename Files Using mv

```bash
ls *.txt | xargs -I {} mv {} {}.bak
```

## 6. Copy .conf Files to a Backup Directory

```bash
find /etc -type f -name "*.conf" | xargs -I {} cp {} /backup_path
```

---

# find Command

The `find` command is used to search for files and directories and perform operations on them.

It supports searching by:

- Name
- Type
- Permissions
- Owner
- Creation date
- Modification date
- Access time
- Size

Using `-exec`, other commands can be executed on the files found.

### Syntax

```bash
find <location_to_search> [options]
```

---

## Combine Multiple Conditions

```bash
find . -name "*.txt" -and -mtime -7
```

Searches for `.txt` files modified within the last 7 days.

---

## Search for Multiple Filenames

Using the logical OR operator (`-o`):

```bash
find . -name "file1.txt" -o -name "file2.txt"
```

```bash
find . -name "file*" -o -name "new*"
```

### Using Regex

```bash
find . -regex '.*\(file1\.txt\|file2\.txt\)'
```

---

# Common Interview Examples

## 1. Search File with Specific Name

```bash
find . -name file.txt
```

---

## 2. Search File with Specific Name (Ignore Case)

```bash
find . -iname file.txt
```

---

## 3. Search Files in Multiple Directories

```bash
find . /home /user -name file.txt
```

---

## 4. Search Only Files

```bash
find . -type f -iname file.txt
```

---

## 5. Search Only Directories

```bash
find . -type d -iname file.txt
```

---

## 6. Search for Empty Files and Directories

```bash
find . -empty
```

---

## 7. Search Files with Specific Permissions

```bash
find . -perm 655
```

---

## 8. Search Text Within Multiple Files

```bash
find . -type f -name "*.txt" -exec grep 'search_string' {} \;
```

---

## 9. Find Files by Last Modification Time

### Modified Exactly 1 Day Ago

```bash
find . -mtime 1
```

### Modified Within Last 7 Days

```bash
find . -mtime -7
```

### Modified Between 50 and 100 Days Ago

```bash
find . -mtime +50 -mtime -100
```

---

## 10. Find Files Accessed 50 Days Ago

```bash
find . -atime 50
```

---

## 11. Find Files Modified Within Last 1 Hour

```bash
find / -mmin -60
```

---

## 12. Find Files Accessed Within Last 1 Hour

```bash
find / -amin -60
```

---

## 13. Find Files Larger Than a Specific Size

```bash
find . -size +100M
```

Examples:

```bash
find . -size +1G
find . -size +500M
find . -size +10k
```

---

# Quick Revision

| Operator/Command | Purpose |
|-----------------|---------|
| `;` | Execute commands sequentially |
| `&&` | Execute next command only if previous succeeds |
| `||` | Execute next command only if previous fails |
| `|` | Pass output of one command as input to another |
| `xargs` | Convert standard input into command arguments |
| `find` | Search files and directories based on conditions |
