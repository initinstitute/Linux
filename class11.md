# 📘 Class 11 Notes - Git Log, Git Cherry-Pick, Git Reset, Git Revert

## 1. `git log`

The `git log` command is used to view the commit history of a repository.

### Syntax

```bash
git log
```

### Example

```bash
git log
```

### Sample Output

```bash
commit a1b2c3d4e5f6
Author: Harish
Date: Tue Jun 18 10:00:00 2026

Added login feature
```

### Common Options

#### View commits in one line

```bash
git log --oneline
```

**Output:**

```bash
a1b2c3d Added login feature
b2c3d4e Fixed bug
c3d4e5f Updated README
```

#### Show last 5 commits

```bash
git log -5
```

#### Show graphical branch history

```bash
git log --oneline --graph --all
```

**Example:**

```bash
* 9f8e7d6 Added Docker file
|\
| * 6a5b4c3 Feature branch commit
|/
* 1a2b3c4 Initial commit
```

---

## 2. `git cherry-pick`

The `git cherry-pick` command copies a specific commit from one branch and applies it to another branch.

### When to Use?

* Need only one commit from another branch.
* Don't want to merge the entire branch.

### Example Scenario

Current Branch:

```bash
main
```

Another Branch:

```bash
feature
```

Commit in feature branch:

```bash
a1b2c3d Added Login Page
```

Switch to main:

```bash
git checkout main
```

Apply the commit:

```bash
git cherry-pick a1b2c3d
```

Result:

```text
main branch now contains the "Added Login Page" commit.
```

### View Commit IDs

```bash
git log --oneline
```

### Cherry-pick Multiple Commits

```bash
git cherry-pick commit1 commit2 commit3
```

### Cherry-pick a Range of Commits

```bash
git cherry-pick commit1^..commit5
```

### If Conflict Occurs

Resolve conflicts and continue:

```bash
git add .
git cherry-pick --continue
```

Abort cherry-pick:

```bash
git cherry-pick --abort
```

---

## 3. `git reset`

The `git reset` command moves the HEAD pointer and can remove commits or unstage files.

### Types of Reset

### A. Soft Reset

Removes commit but keeps changes staged.

```bash
git reset --soft HEAD~1
```

#### Before

```text
Commit3 ← HEAD
Commit2
Commit1
```

#### After

```text
Commit2 ← HEAD
Commit1
```

Changes from Commit3 remain staged.

---

### B. Mixed Reset (Default)

Removes commit and unstages changes.

```bash
git reset HEAD~1
```

or

```bash
git reset --mixed HEAD~1
```

Changes remain in the working directory.

---

### C. Hard Reset

Removes commit and deletes changes permanently.

```bash
git reset --hard HEAD~1
```

⚠️ **Warning:** This permanently deletes uncommitted changes.

---

### Reset to a Specific Commit

Find commit ID:

```bash
git log --oneline
```

Reset:

```bash
git reset --hard a1b2c3d
```

---

### Unstage Files

Before:

```bash
git add file.txt
```

Unstage:

```bash
git reset file.txt
```

---

## 4. `git revert`

The `git revert` command creates a new commit that undoes the changes made by a previous commit.

### Why Use Revert?

* Safe for shared repositories.
* Does not remove commit history.
* Recommended when commits are already pushed.

### Example

Commit History:

```text
A → B → C
```

Suppose commit C contains a bug.

Revert it:

```bash
git revert C
```

New History:

```text
A → B → C → D
```

Where:

```text
D = Undo changes made in C
```

### Revert Latest Commit

```bash
git revert HEAD
```

### Revert Specific Commit

```bash
git revert a1b2c3d
```

### Revert Multiple Commits

```bash
git revert commit1 commit2
```

---

# Git Reset vs Git Revert

| Feature                  | git reset | git revert |
| ------------------------ | --------- | ---------- |
| Removes commit history   | ✅ Yes     | ❌ No       |
| Creates new commit       | ❌ No      | ✅ Yes      |
| Safe for shared branches | ❌ No      | ✅ Yes      |
| Rewrites history         | ✅ Yes     | ❌ No       |
| Recommended after push   | ❌ No      | ✅ Yes      |

---

# Quick Reference

### View Commit History

```bash
git log
git log --oneline
```

### Copy Commit from Another Branch

```bash
git cherry-pick <commit-id>
```

### Remove Last Commit (Keep Changes Staged)

```bash
git reset --soft HEAD~1
```

### Remove Last Commit (Keep Changes Unstaged)

```bash
git reset HEAD~1
```

### Remove Last Commit Permanently

```bash
git reset --hard HEAD~1
```

### Undo a Commit Safely

```bash
git revert <commit-id>
```

---

# Interview Question

### Q: When should you use `git reset` and when should you use `git revert`?

**Answer:**

* Use **`git reset`** when the commit has **not been pushed** and you want to remove or modify history.
* Use **`git revert`** when the commit has **already been pushed/shared**, as it safely creates a new commit that undoes the changes without rewriting history.
