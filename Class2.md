# Day 02 - Linux File Management Commands

## Topics Covered

* `rm` - Remove Files & Directories
* `cp` - Copy Files & Directories
* `mv` - Move & Rename Files/Directories

---

# 1. rm Command (Remove Files and Directories)

The `rm` command is used to delete files and directories in Linux.

> ⚠️ Warning: Deleted files cannot be recovered easily. Use this command carefully.

## Delete a File

### Syntax

```bash
rm <file_name>
```

### Example

```bash
rm notes.txt
```

## Delete a Directory

The `rm` command alone cannot remove directories.

Use the `-r` option to delete a directory and all its contents recursively.

### Syntax

```bash
rm -r <directory_name>
```

### Example

```bash
rm -r test
```

---

# 2. cp Command (Copy Files and Directories)

The `cp` command is used to copy files and directories from one location to another.

## Syntax

```bash
cp [OPTIONS] <SOURCE> <DESTINATION>
```

## Copy a File to Another Location

### Example

```bash
cp file1.txt /home/ubuntu/Dir1/
```

## Copy and Rename a File

### Example

```bash
cp file1.txt file2.txt
```

This creates a copy of `file1.txt` with the name `file2.txt`.

## Copy a Directory Recursively

Use the `-r` option to copy a directory and all its contents.

### Example

```bash
cp -r dir1/ dir2/
```

## Copy Multiple Files to a Directory

### Example

```bash
cp file1.txt file2.txt file3.txt file4.txt /home/user/Dir1/
```

## Prompt Before Overwriting a File

Use the `-i` option for confirmation before replacing an existing file.

### Example

```bash
cp -i file1.txt /home/user/Dir1/
```

### Output

```text
cp: overwrite '/home/user/Dir1/file1.txt'? (y/n)
```

---

# 3. mv Command (Move or Rename Files and Directories)

The `mv` command is used to:

* Move files and directories
* Rename files and directories

## Syntax

```bash
mv [OPTIONS] <SOURCE> <DESTINATION>
```

## Rename a File

### Syntax

```bash
mv <old_filename> <new_filename>
```

### Example

```bash
mv file1.txt notes.txt
```

## Rename a Directory

### Syntax

```bash
mv <old_directory> <new_directory>
```

### Example

```bash
mv test DevOps
```

## Move a File to Another Location

### Example

```bash
mv file1.txt /home/ubuntu/dir1/
```

This is similar to **Cut and Paste** in Windows.

## Move Multiple Files to a Directory

### Example

```bash
mv file1.txt file2.txt file3.txt /home/user/backup/
```

---

# Practice Tasks

## Task 1 - Create Files

```bash
touch file1.txt file2.txt file3.txt
```

## Task 2 - Copy a File

```bash
mkdir backup
cp file1.txt backup/
```

## Task 3 - Copy and Rename a File

```bash
cp file1.txt file1_backup.txt
```

## Task 4 - Move a File

```bash
mv file2.txt backup/
```

## Task 5 - Rename a File

```bash
mv file3.txt project.txt
```

## Task 6 - Delete a File

```bash
rm project.txt
```

## Task 7 - Delete a Directory

```bash
rm -r backup
```

---

# Quick Summary Table

| Command | Purpose                  | Example                   |
| ------- | ------------------------ | ------------------------- |
| `rm`    | Delete files             | `rm file.txt`             |
| `rm -r` | Delete directories       | `rm -r test`              |
| `cp`    | Copy files               | `cp file1.txt backup/`    |
| `cp -r` | Copy directories         | `cp -r dir1 dir2`         |
| `cp -i` | Copy with confirmation   | `cp -i file1.txt backup/` |
| `mv`    | Move files/directories   | `mv file1.txt backup/`    |
| `mv`    | Rename files/directories | `mv old.txt new.txt`      |

---

# Day 02 Hands-On Lab

```bash
# Create files
touch file1.txt file2.txt file3.txt

# Create backup directory
mkdir backup

# Copy file
cp file1.txt backup/

# Copy and rename
cp file1.txt file1_backup.txt

# Move file
mv file2.txt backup/

# Rename file
mv file3.txt project.txt

# Delete file
rm project.txt

# Delete directory
rm -r backup
```

---

# Day 02 Completed Topics

✅ Linux File Management

✅ rm Command

✅ cp Command

✅ mv Command

✅ Copying Files and Directories

✅ Moving and Renaming Files

✅ Deleting Files and Directories

✅ Hands-on Practice Tasks


