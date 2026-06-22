# Class Notes - Git Basic Commands

# 1. git init

## Purpose
The `git init` command initializes a new Git repository in the current directory.

## Syntax

```bash
git init
```

## Example

```bash
mkdir myproject
cd myproject
git init
```

## Output

```bash
Initialized empty Git repository in /home/user/myproject/.git/
```

## Explanation

- Creates a hidden `.git` directory.
- Starts tracking the project using Git.
- Used when creating a new project from scratch.

## Diagram

```text
Project Folder
│
├── file1.txt
├── file2.txt
└── .git/    ← Created by git init
```

---

# 2. git clone

## Purpose

The `git clone` command creates a copy of an existing remote repository on your local machine.

## Syntax

```bash
git clone <repository_url>
```

## Example

```bash
git clone https://github.com/user/project.git
```

## Explanation

- Downloads the repository.
- Copies all branches, commits, and files.
- Automatically connects the local repository to the remote repository.

## Diagram

```text
GitHub Repository
        │
        ▼
git clone
        │
        ▼
Local Repository
```

---

# 3. git branch

## Purpose

Used to create, list, or manage branches.

## Syntax

```bash
git branch
```

## Example

```bash
git branch
```

## Output

```bash
* main
  dev
  testing
```

## Explanation

- Shows all local branches.
- `*` indicates the currently active branch.

## Create a New Branch

```bash
git branch feature-login
```

## Diagram

```text
main
 │
 ├── dev
 │
 └── feature-login
```

---

# 4. git checkout -b

## Purpose

Creates a new branch and immediately switches to it.

## Syntax

```bash
git checkout -b <branch_name>
```

## Example

```bash
git checkout -b feature-login
```

## Explanation

Equivalent to:

```bash
git branch feature-login
git checkout feature-login
```

## Diagram

```text
Before:
main

After:
main
 │
 └── feature-login ← Current Branch
```

---

# 5. git checkout

## Purpose

Switches from one branch to another.

## Syntax

```bash
git checkout <branch_name>
```

## Example

```bash
git checkout main
```

## Explanation

Moves your working directory to the selected branch.

## Diagram

```text
Current Branch: feature-login

git checkout main

Current Branch: main
```

---

# 6. git add .

## Purpose

Adds all modified and new files to the staging area.

## Syntax

```bash
git add .
```

## Example

```bash
git add .
```

## Explanation

- Stages all changes in the current directory.
- Prepares files for commit.

## Workflow

```text
Working Directory
       │
       ▼
   git add .
       │
       ▼
 Staging Area
```

---

# 7. git commit -m ""

## Purpose

Saves staged changes into Git history.

## Syntax

```bash
git commit -m "commit message"
```

## Example

```bash
git commit -m "Added login page"
```

## Explanation

- Creates a snapshot of current changes.
- Message should clearly describe the changes made.

## Workflow

```text
Staging Area
      │
      ▼
git commit -m "message"
      │
      ▼
Git Repository
```

---

# 8. git push origin

## Purpose

Uploads local commits to a remote repository.

## Syntax

```bash
git push origin <branch_name>
```

## Example

```bash
git push origin main
```

## Explanation

- Sends local commits to GitHub.
- Updates the specified branch in the remote repository.

## Diagram

```text
Local Repository
       │
       ▼
git push origin main
       │
       ▼
GitHub Repository
```

---

# 9. git push -u origin

## Purpose

Pushes a branch and sets the upstream (tracking) branch.

## Syntax

```bash
git push -u origin <branch_name>
```

## Example

```bash
git push -u origin feature-login
```

## Explanation

- Pushes the branch to GitHub.
- Creates a connection between local and remote branches.
- Future pushes can use only `git push`.

## Diagram

```text
feature-login (Local)
          │
          ▼
git push -u origin feature-login
          │
          ▼
feature-login (GitHub)

Tracking Relationship Created
```

---

# 10. git push

## Purpose

Pushes changes to the tracked remote branch.

## Syntax

```bash
git push
```

## Example

```bash
git push
```

## Explanation

- Works only if an upstream branch is already configured.
- Usually used after `git push -u origin <branch_name>`.

## Workflow

```text
Local Commit
      │
      ▼
 git push
      │
      ▼
Remote Repository
```

---

# Complete Git Workflow

```text
Create Project
      │
      ▼
git init
      │
      ▼
Create Files
      │
      ▼
git add .
      │
      ▼
git commit -m "Initial Commit"
      │
      ▼
git checkout -b feature-login
      │
      ▼
Make Changes
      │
      ▼
git add .
      │
      ▼
git commit -m "Added Login Feature"
      │
      ▼
git push -u origin feature-login
      │
      ▼
Future Changes
      │
      ▼
git add .
git commit -m "Updated Login Page"
git push
```

---

# Quick Reference

| Command | Purpose |
|----------|---------|
| `git init` | Initialize a new Git repository |
| `git clone <url>` | Copy a remote repository to the local machine |
| `git branch` | List branches |
| `git branch <name>` | Create a new branch |
| `git checkout <branch>` | Switch branch |
| `git checkout -b <branch>` | Create and switch branch |
| `git add .` | Stage all changes |
| `git commit -m "message"` | Save changes to Git history |
| `git push origin <branch>` | Push a branch to GitHub |
| `git push -u origin <branch>` | Push and set upstream branch |
| `git push` | Push to tracked remote branch |

---

# Interview Question

## Q: What is the difference between `git push origin branch_name` and `git push -u origin branch_name`?

### Answer

- `git push origin branch_name` → Pushes the branch only once.
- `git push -u origin branch_name` → Pushes the branch and sets an upstream tracking relationship, allowing future pushes using only `git push`.
