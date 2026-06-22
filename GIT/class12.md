# 📘 Class 12 Notes - Git Fetch, Git Stash, Git Merge, Git Rebase & Conflict Resolution

---

# 1. Git Fetch

## What is Git Fetch?

`git fetch` downloads the latest changes from the remote repository without modifying your local files.

It updates the remote-tracking branches but does not merge the changes into your current branch.

### Syntax

```bash
git fetch
```

### Example

```bash
git fetch origin
```

### What Happens?

Before Fetch:

```text
Remote (GitHub)
      |
      |  New commits available
      |
Local Repository
```

After Fetch:

```text
Remote (GitHub)
      |
      |  Downloaded
      |
Local Repository
      |
      └── origin/main updated
```

Your working directory remains unchanged.

### Check Downloaded Changes

```bash
git log main..origin/main
```

### Fetch All Branches

```bash
git fetch --all
```

### Difference Between Fetch and Pull

| Command | Downloads Changes | Merges Changes |
|----------|------------------|---------------|
| git fetch | ✅ Yes | ❌ No |
| git pull | ✅ Yes | ✅ Yes |

---

# 2. Git Stash

## What is Git Stash?

`git stash` temporarily saves uncommitted changes so you can work on something else without committing unfinished work.

### Scenario

You are working on a feature and suddenly need to switch branches.

Current Status:

```bash
git status
```

Output:

```text
modified: app.py
modified: test.py
```

Instead of committing unfinished work:

```bash
git stash
```

Output:

```text
Saved working directory and index state
```

Your working directory becomes clean.

## View Stashes

```bash
git stash list
```

Output:

```text
stash@{0}: WIP on main
stash@{1}: WIP on feature
```

## Apply Stashed Changes

```bash
git stash apply
```

or

```bash
git stash apply stash@{0}
```

Changes are restored but stash remains.

## Apply and Remove Stash

```bash
git stash pop
```

Output:

```text
Dropped refs/stash@{0}
```

## Delete a Specific Stash

```bash
git stash drop stash@{0}
```

## Delete All Stashes

```bash
git stash clear
```

## Stash Including Untracked Files

```bash
git stash -u
```

## Stash Workflow

```text
Working Changes
       |
       v
git stash
       |
       v
Temporary Storage
       |
       v
git stash pop
       |
       v
Restore Changes
```

---

# 3. Git Merge

## What is Git Merge?

Git merge combines changes from one branch into another.

### Example Setup

```text
main
 |
 └── Commit A
      |
      └── Commit B

feature
 |
 └── Commit C
      |
      └── Commit D
```

Switch to main:

```bash
git checkout main
```

Merge feature branch:

```bash
git merge feature
```

Result:

```text
main
 |
 └── A
      |
      └── B
            |
            └── Merge Commit
                  |
                  ├── C
                  └── D
```

## Fast Forward Merge

Before:

```text
A---B---C (main)
         \
          D---E (feature)
```

```bash
git checkout main
git merge feature
```

After:

```text
A---B---C---D---E (main)
```

No merge commit is created.

## Three-Way Merge

Before:

```text
       D---E (feature)
      /
A---B---C (main)
```

After:

```text
       D---E
      /     \
A---B---C----M
```

`M` = Merge Commit

## View Merge History

```bash
git log --oneline --graph
```

---

# 4. Git Rebase

## What is Git Rebase?

Rebase moves one branch on top of another branch, creating a cleaner and linear history.

### Example

Before:

```text
main
 |
 A---B---C

feature
 |
 A---B---D---E
```

Update feature branch:

```bash
git checkout feature
git rebase main
```

After:

```text
main
 |
 A---B---C

feature
 |
 A---B---C---D'---E'
```

Git replays feature commits on top of main.

## Rebase Command

```bash
git checkout feature
git rebase main
```

## Benefits of Rebase

- ✅ Cleaner history
- ✅ No unnecessary merge commits
- ✅ Easier to read commit logs
- ✅ Linear project timeline

## Merge vs Rebase

| Feature | Merge | Rebase |
|----------|---------|---------|
| Keeps History | ✅ Yes | ❌ No |
| Creates Merge Commit | ✅ Yes | ❌ No |
| Easier for Beginners | ✅ Yes | ❌ No |
| Cleaner Log | ❌ No | ✅ Yes |
| Safe for Shared Branches | ✅ Yes | ❌ No |

### Visual Difference

#### Merge

```text
A---B---C------M
     \        /
      D------E
```

#### Rebase

```text
A---B---C---D'---E'
```

---

# 5. Git Conflicts

## What is a Merge Conflict?

A conflict occurs when Git cannot automatically determine which changes should be kept.

### Example

Developer 1 changes:

```python
name = "Harish"
```

Developer 2 changes:

```python
name = "Reddy"
```

Both modify the same line.

When merging:

```bash
git merge feature
```

Git shows:

```text
CONFLICT (content): Merge conflict in app.py
Automatic merge failed
```

---

# 6. Understanding Conflict Markers

Git inserts conflict markers inside the file.

Example:

```text
<<<<<<< HEAD
name = "Harish"
=======
name = "Reddy"
>>>>>>> feature
```

Meaning:

```text
<<<<<<< HEAD
Current branch version

=======
Incoming branch version

>>>>>>> feature
```

---

# 7. Resolving Merge Conflicts

### Step 1: Open the File

```bash
vi app.py
```

Conflict:

```text
<<<<<<< HEAD
name = "Harish"
=======
name = "Reddy"
>>>>>>> feature
```

### Step 2: Decide Final Content

```python
name = "Harish Reddy"
```

Remove all conflict markers.

### Step 3: Add File

```bash
git add app.py
```

### Step 4: Complete Merge

```bash
git commit
```

Git creates a merge commit.

---

# 8. Resolving Rebase Conflicts

During rebase:

```bash
git rebase main
```

Conflict:

```text
CONFLICT (content): Merge conflict in app.py
```

### Fix the File

```python
name = "Harish Reddy"
```

### Add Changes

```bash
git add app.py
```

### Continue Rebase

```bash
git rebase --continue
```

### Abort Rebase

```bash
git rebase --abort
```

### Skip Current Commit

```bash
git rebase --skip
```

---

# 9. Conflict Resolution Workflow

## Merge Conflict Workflow

```text
git merge feature
        |
        v
Conflict Found
        |
        v
Edit File
        |
        v
git add file
        |
        v
git commit
        |
        v
Merge Complete
```

## Rebase Conflict Workflow

```text
git rebase main
        |
        v
Conflict Found
        |
        v
Edit File
        |
        v
git add file
        |
        v
git rebase --continue
        |
        v
Rebase Complete
```

---

# 🎯 Important Commands Summary

```bash
# Fetch latest changes
git fetch

# Fetch all branches
git fetch --all

# Save changes temporarily
git stash

# View stashes
git stash list

# Restore stash
git stash apply

# Restore and remove stash
git stash pop

# Delete stash
git stash drop stash@{0}

# Merge branch
git merge feature

# Rebase branch
git rebase main

# Continue rebase after conflict
git rebase --continue

# Abort rebase
git rebase --abort

# Check commit graph
git log --oneline --graph --all

# Check status
git status
```

---

# 📌 Interview Questions

### Q1. What is the difference between `git fetch` and `git pull`?

**Answer:**

- `git fetch` downloads changes from the remote repository.
- `git pull` downloads and immediately merges those changes.

### Q2. What is `git stash` used for?

**Answer:**

It temporarily saves uncommitted changes so you can switch branches or work on another task without committing unfinished work.

### Q3. What is the difference between Merge and Rebase?

**Answer:**

- Merge combines branches and creates a merge commit.
- Rebase replays commits on top of another branch to create a linear history.

### Q4. What causes a merge conflict?

**Answer:**

When two branches modify the same line of the same file and Git cannot automatically decide which version to keep.

### Q5. How do you resolve a conflict during rebase?

**Answer:**

```bash
# Fix the file
git add <file>

# Continue rebase
git rebase --continue
```

To cancel the rebase:

```bash
git rebase --abort
```
