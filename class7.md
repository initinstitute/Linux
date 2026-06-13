# Linux Commands - CUT, AWK, TAC, TAR

## CUT

The `cut` command in UNIX is used to extract specific columns or fields from each line of a file and display the result.

### Syntax
```bash
cut -d "delimiter" -f field_number file.txt
```

### Examples

#### 1. Print the first column using space as a delimiter
```bash
cut -d " " -f 1 state.txt
```

#### 2. Print columns 1 to 4 using space as a delimiter
```bash
cut -d " " -f 1-4 state.txt
```

---

## AWK

The `awk` command is a powerful text-processing utility used for pattern scanning and processing data in files.

### Sample Input File

```bash
cat > employee.txt
Ajay manager account 45000
Sunil clerk account 25000
Varun manager sales 50000
Amit manager account 47000
```

### 1. Print the First Column

```bash
awk '{print $1}' employee.txt
```

Output:
```bash
Ajay
Sunil
Varun
Amit
```

### 2. Print the Last Column Using NF (Number of Fields)

```bash
awk '{print $NF}' employee.txt
```

Output:
```bash
45000
25000
50000
47000
```

### 3. Print Lines Matching a Specific Pattern

Print all lines containing the word "ERROR":

```bash
awk '/ERROR/ {print}' logfile.txt
```

### 4. Print All Columns Except the First Column

```bash
awk '{$1=""; print $0}' file.txt
```

### 5. Print All Columns Except the Last Column

```bash
awk '{$NF=""; print $0}' file.txt
```

---

## TAC

The `tac` command in Linux displays the contents of a file in reverse order (last line first). It is the opposite of the `cat` command.

### Syntax

```bash
tac file.txt
```

### Example

Input (`file.txt`):

```text
Line1
Line2
Line3
```

Command:

```bash
tac file.txt
```

Output:

```text
Line3
Line2
Line1
```

---

## TAR

The `tar` command is used to create, extract, and manage archive files in Linux.

### Create a Tar Archive

```bash
tar -cvf archive.tar file1 file2
```

- `c` → Create archive
- `v` → Verbose output
- `f` → Archive filename

### Extract a Tar Archive

```bash
tar -xvf archive.tar
```

- `x` → Extract archive

### Create a Compressed Tar.gz Archive

```bash
tar -czvf archive.tar.gz directory/
```

- `z` → Compress using gzip

### Extract a Tar.gz Archive

```bash
tar -xzvf archive.tar.gz
```

### List Contents of a Tar Archive

```bash
tar -tvf archive.tar
```

### Example

Create an archive:

```bash
tar -cvf backup.tar project/
```

Extract the archive:

```bash
tar -xvf backup.tar
```

---
