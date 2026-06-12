# Day 5 Notes - SED (Stream Editor)

## What is SED?

**SED (Stream Editor)** is a powerful UNIX/Linux text processing tool used for:

- Searching text
- Finding and replacing text
- Inserting lines
- Deleting lines
- Editing files automatically

SED reads input line by line and performs the specified operation.

---

## Basic Syntax

```bash
sed 'operation' filename
```

Example:

```bash
sed 's/old_text/new_text/' file.txt
```

---

## Common SED Commands

### 1. Replace a String

Replace the first occurrence of a word in every line.

```bash
sed 's/unix/linux/' file.txt
```

Example:

Before:

```text
Unix is easy to learn. Unix is powerful.
```

After:

```text
linux is easy to learn. Unix is powerful.
```

---

### 2. Replace the Nth Occurrence of a Pattern

Replace only the 2nd occurrence in each line.

```bash
sed 's/unix/linux/2' file.txt
```

---

### 3. Replace All Occurrences

Use the **g (global)** flag.

```bash
sed 's/unix/linux/g' file.txt
```

Example:

Before:

```text
unix unix unix
```

After:

```text
linux linux linux
```

---

### 4. Replace from Nth Occurrence to All Remaining Occurrences

Replace from the 3rd occurrence onward.

```bash
sed 's/unix/linux/3g' file.txt
```

---

### 5. Replace Text on a Specific Line Number

Replace only on line 3.

```bash
sed '3s/unix/linux/g' file.txt
```

---

### 6. Print Only the Replaced Lines

Use **-n** with the **p** option.

```bash
sed -n 's/unix/linux/p' file.txt
```

Only lines where substitution happened will be displayed.

---

## In-Place Editing

Modify the file directly without displaying output.

```bash
sed -i 's/old_text/new_text/g' filename
```

Example:

```bash
sed -i 's/unix/linux/g' file.txt
```

---

## Case-Insensitive Replacement

Replace regardless of uppercase or lowercase.

```bash
sed 's/old_text/new_text/I' filename
```

Example:

```bash
sed 's/unix/linux/I' file.txt
```

Matches:

```text
unix
Unix
UNIX
```

---

## Multiple Replacements

Use the **-e** option.

```bash
sed -e 's/old_text1/new_text1/' \
    -e 's/old_text2/new_text2/' filename
```

Example:

```bash
sed -e 's/unix/linux/' \
    -e 's/os/operating_system/' file.txt
```

---

## Remove Blank Lines

Delete empty lines.

```bash
sed '/^$/d' filename
```

---

## Deleting Lines

### Delete a Specific Line

Delete line 5.

```bash
sed '5d' file.txt
```

### Delete the Last Line

```bash
sed '$d' file.txt
```

### Delete a Range of Lines

Delete lines 3 to 6.

```bash
sed '3,6d' file.txt
```

### Delete from Nth Line to Last Line

Delete line 12 onwards.

```bash
sed '12,$d' file.txt
```

### Delete Pattern Matching Lines

Delete lines containing a specific pattern.

```bash
sed '/pattern/d' file.txt
```

Example:

```bash
sed '/unix/d' file.txt
```

---

## Printing Specific Lines

### Print Line Number 2

```bash
sed -n '2p' file.txt
```

### Print a Range of Lines

```bash
sed -n '3,5p' file.txt
```

---

## Insert and Append Text

### Insert Text Before a Line

Insert before line 2.

```bash
sed '2i\This is the inserted line.' file.txt
```

Output:

```text
Line1
This is the inserted line.
Line2
Line3
```

### Append Text After a Line

Append after line 2.

```bash
sed '2a\This is the appended line.' file.txt
```

Output:

```text
Line1
Line2
This is the appended line.
Line3
```

---

## Sample Input File (file.txt)

```text
Unix is a great os. Unix is open source. Unix is a free os.
learn operating systems.
Unix Linux which one you choose.
Unix is easy to learn. Unix is a multiuser os. Learn unix .unix is a powerful.
```

---

## Frequently Asked Interview Questions

### Replace first occurrence

```bash
sed 's/unix/linux/' file.txt
```

### Replace second occurrence

```bash
sed 's/unix/linux/2' file.txt
```

### Replace all occurrences

```bash
sed 's/unix/linux/g' file.txt
```

### Replace only on line 3

```bash
sed '3s/unix/linux/g' file.txt
```

### Delete line 5

```bash
sed '5d' file.txt
```

### Delete last line

```bash
sed '$d' file.txt
```

### Delete lines 3 to 6

```bash
sed '3,6d' file.txt
```

### Print line 2

```bash
sed -n '2p' file.txt
```

### Remove blank lines

```bash
sed '/^$/d' file.txt
```

### Insert before line 2

```bash
sed '2i\This is the inserted line.' file.txt
```

### Append after line 2

```bash
sed '2a\This is the appended line.' file.txt
```

---

## Quick Revision

| Operation | Command |
|------------|----------|
| Replace first occurrence | `sed 's/unix/linux/' file.txt` |
| Replace all occurrences | `sed 's/unix/linux/g' file.txt` |
| Replace 2nd occurrence | `sed 's/unix/linux/2' file.txt` |
| Delete line 5 | `sed '5d' file.txt` |
| Delete last line | `sed '$d' file.txt` |
| Delete lines 3-6 | `sed '3,6d' file.txt` |
| Delete blank lines | `sed '/^$/d' file.txt` |
| Print line 2 | `sed -n '2p' file.txt` |
| Insert before line 2 | `sed '2i\Text' file.txt` |
| Append after line 2 | `sed '2a\Text' file.txt` |
| Modify file directly | `sed -i 's/old/new/g' file.txt` |
| Case-insensitive replace | `sed 's/old/new/I' file.txt` |

---

## Key Points

- SED stands for **Stream Editor**.
- Used for text processing and automation.
- Supports search, replace, insert, append, and delete operations.
- `g` = Global replacement.
- `-i` = Modify file directly.
- `-n` = Suppress default output.
- `p` = Print matching lines.
- Frequently used in Shell Scripting, DevOps, and Linux Administration.
