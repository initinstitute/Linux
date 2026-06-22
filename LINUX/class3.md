# Day 3 - Linux Fundamentals

## Tree Command

The `tree` command is used to display the contents of a directory in a tree-like structure. It recursively lists all files and directories inside a specified directory.

### Install Tree

Before installing any software or tools, refresh the package list to get the latest package information.

```bash
sudo apt update
```

Install the Tree package:

```bash
sudo apt install tree
```

### Basic Usage

Display the current directory structure:

```bash
tree
```

### Useful Tree Command Options

#### Display Only Directories

```bash
tree -d
```

#### List All Files Including Hidden Files

```bash
tree -a
```

#### Limit the Depth of the Tree

Show only 2 levels of directories:

```bash
tree -L 2
```

#### Display Full Path for Each File

```bash
tree -f
```

#### Display Human-Readable File Sizes

```bash
tree -h
```

---

# Vi Editor

The **Vi Editor** is a powerful text editor available in Linux. It works in different modes.

## Opening a File

```bash
vi filename
```

Example:

```bash
vi notes.txt
```

---

## Command Mode

When a file is opened in Vi Editor, it starts in **Command Mode** by default.

In this mode, you cannot directly type content. It is used to execute Vi commands.

### Basic File Operations

#### Save Changes

```bash
:w
```

#### Quit Without Saving

```bash
:q!
```

#### Save and Quit

```bash
:wq!
```

OR

```bash
Press Esc and then Shift + ZZ
```

#### Save to Another File

```bash
:w new_file_name
```

Example:

```bash
:w backup.txt
```

---

## Navigation & Editing Commands

### Undo Last Change

```bash
u
```

### Redo Last Undone Change

```bash
Ctrl + r
```

### Search for a Word

```bash
/word
```

Example:

```bash
/linux
```

### Display Line Numbers

```bash
:set nu
```

### Disable Line Numbers

```bash
:set nu!
```

### Replace a Word Globally

```bash
:s/old_word/new_word/g
```

Example:

```bash
:s/linux/unix/g
```

---

## Cut, Copy, and Paste

### Cut Current Line

```bash
dd
```

### Copy Current Line

```bash
yy
```

### Paste Copied/Cut Content

```bash
p
```

### Delete a Word

```bash
dw
```

### Delete from Cursor to End of Line

```bash
D
```

### Delete from Cursor to End of File

```bash
dG
```

---

## Insert Mode

Insert Mode is used to add or modify content in a file.

### Enter Insert Mode

Press:

```bash
i
```

### Exit Insert Mode

Press:

```bash
Esc
```

After pressing `Esc`, Vi returns to **Command Mode**.

---

# Quick Summary

| Action | Command |
|----------|----------|
| Open File | `vi filename` |
| Enter Insert Mode | `i` |
| Exit Insert Mode | `Esc` |
| Save File | `:w` |
| Quit Without Saving | `:q!` |
| Save and Quit | `:wq!` |
| Undo | `u` |
| Redo | `Ctrl + r` |
| Cut Line | `dd` |
| Copy Line | `yy` |
| Paste | `p` |
| Show Line Numbers | `:set nu` |
| Search Word | `/word` |
| Replace Word | `:s/old/new/g` |

---

## Practice Exercise

1. Install the Tree package.
2. Create a directory structure with multiple folders.
3. Display the structure using Tree.
4. Create a file using Vi Editor.
5. Add content using Insert Mode.
6. Save and quit the file.
7. Copy, cut, and paste lines.
8. Search and replace words in the file.
