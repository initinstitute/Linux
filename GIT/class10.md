# Day 9 - Git Configuration, Credentials, Aliases, Git Pull, Cherry-Pick & GitHub Token

## Configure User Information

Git uses your name and email information when creating commits.

### Set Username
```bash
git config --global user.name "<username>"
```

Example:
```bash
git config --global user.name "Harish Reddy"
```

### Set Email
```bash
git config --global user.email "<user_email>"
```

Example:
```bash
git config --global user.email "harish@example.com"
```

### Verify Configuration
```bash
git config --global --list
```

---

# Configure Default Editor

Git opens an editor when writing commit messages, merge messages, etc.

### Set VI as Default Editor
```bash
git config --global core.editor "vi"
```

### Set Nano as Default Editor
```bash
git config --global core.editor "nano"
```

### Check Current Editor
```bash
git config --global core.editor
```

---

# Configure Git to Store Credentials

Instead of entering your GitHub username and token every time, Git can store them.

## Store Credentials Permanently

```bash
git config --global credential.helper store
```

When you authenticate for the first time, Git saves your credentials locally.

### Credential File Location

Linux:
```bash
~/.git-credentials
```

View stored credentials:
```bash
cat ~/.git-credentials
```

---

## Cache Credentials Temporarily

Stores credentials in memory.

```bash
git config --global credential.helper cache
```

Default cache time:
- 15 minutes

### Cache for 1 Hour

```bash
git config --global credential.helper 'cache --timeout=3600'
```

---

## Remove Credential Helper

```bash
git config --global --unset credential.helper
```

Verify:

```bash
git config --global --list
```

---

# Enable Colored Output

Makes Git output easier to read.

```bash
git config --global color.ui auto
```

Examples:
- Green → Added files
- Red → Deleted files
- Yellow → Modified files

---

# Create Git Aliases

Aliases are shortcuts for frequently used Git commands.

## Create Aliases

### Status
```bash
git config --global alias.st "status"
```

### Checkout
```bash
git config --global alias.co "checkout"
```

### Branch
```bash
git config --global alias.br "branch"
```

### Commit
```bash
git config --global alias.cm "commit -m"
```

---

## Use Aliases

Instead of:

```bash
git status
```

Use:

```bash
git st
```

Instead of:

```bash
git checkout main
```

Use:

```bash
git co main
```

Instead of:

```bash
git branch
```

Use:

```bash
git br
```

Instead of:

```bash
git commit -m "Added feature"
```

Use:

```bash
git cm "Added feature"
```

---

# View All Global Configurations

List all global Git settings:

```bash
git config --global --list
```

Example Output:

```text
user.name=Harish Reddy
user.email=harish@example.com
core.editor=nano
credential.helper=store
color.ui=auto
alias.st=status
alias.co=checkout
alias.br=branch
alias.cm=commit -m
```

---

# Git Pull

The `git pull` command fetches changes from a remote repository and merges them into your current branch.

### Syntax

```bash
git pull
```

### Pull Specific Branch

```bash
git pull origin main
```

### Pull Another Branch

```bash
git pull origin dev
```

### What Happens?

Git performs:

```bash
git fetch
git merge
```

behind the scenes.

---

# Git Cherry-Pick

Cherry-pick allows you to copy a specific commit from one branch and apply it to another branch.

### Syntax

```bash
git cherry-pick <commit-id>
```

Example:

```bash
git cherry-pick a1b2c3d
```

### Steps

1. Switch to target branch

```bash
git checkout main
```

2. Cherry-pick the commit

```bash
git cherry-pick a1b2c3d
```

3. Push changes

```bash
git push origin main
```

### View Commit IDs

```bash
git log --oneline
```

Example:

```text
a1b2c3d Added login feature
f4e5g6h Fixed bug
```

---

# GitHub Personal Access Token (PAT)

GitHub no longer supports password authentication for Git operations.

Instead, use a Personal Access Token (PAT).

---

## Step 1: Sign In to GitHub

Login to your GitHub account.

---

## Step 2: Open Settings

Click:

```text
Profile Picture
    ↓
Settings
```

---

## Step 3: Developer Settings

Scroll to bottom-left:

```text
Settings
    ↓
Developer Settings
```

---

## Step 4: Personal Access Tokens

Navigate:

```text
Developer Settings
    ↓
Personal Access Tokens
    ↓
Tokens (Classic)
```

---

## Step 5: Generate New Token

Click:

```text
Generate New Token
    ↓
Generate New Token (Classic)
```

---

## Step 6: Configure Token

### Note

```text
Git Token
```

### Expiration

Choose:

```text
30 days
90 days
Custom
No expiration
```

### Select Scopes

For Git operations select:

```text
repo
workflow (optional)
read:org (optional)
```

Minimum required:

```text
repo
```

---

## Step 7: Generate Token

Click:

```text
Generate Token
```

GitHub displays the token only once.

Example:

```text
ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

Copy and save it securely.

---

# Using the Token

When Git asks for credentials:

### Username

```text
Your GitHub Username
```

### Password

Paste:

```text
GitHub Personal Access Token
```

NOT your GitHub password.

---

# Store the Token Permanently

Enable credential storage:

```bash
git config --global credential.helper store
```

Push once:

```bash
git push origin main
```

Enter:

```text
Username: your-github-username
Password: ghp_xxxxxxxxxxxxxxxxx
```

Git saves the token and won't ask again.

---

# Useful Commands Summary

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global core.editor "nano"
git config --global credential.helper store
git config --global color.ui auto

git config --global alias.st "status"
git config --global alias.co "checkout"
git config --global alias.br "branch"
git config --global alias.cm "commit -m"

git config --global --list

git pull origin main

git cherry-pick <commit-id>
```

## Key Takeaways

- Configure username and email before committing.
- Set your preferred editor (vi/nano).
- Store credentials to avoid repeated authentication.
- Use aliases to save typing time.
- `git pull` fetches and merges remote changes.
- `git cherry-pick` copies a specific commit to another branch.
- GitHub Personal Access Tokens are used instead of passwords.
