# 🛠️ Class 7 Notes: Introduction to GitHub & Web UI

Welcome to your ultimate guide on GitHub! This page contains everything you need to know about how GitHub works, how to use it entirely from your web browser (UI), and how to collaborate with your classmates safely.

---

## 🗺️ Module 1: What is GitHub?

Imagine you and your friends are writing a comic book together. If you all try to draw on the exact same piece of paper at the same time, it will get messy, lines will overlap, and someone might accidentally erase your work.

**GitHub** is like a magical, cloud-based notebook. It lets multiple people work on the same project (like code, school reports, or websites) from different computers at the same time without breaking anyone else's work.

### 🔑 Key Vocabulary to Remember
*   **Repository (Repo):** Your main project folder stored online on GitHub.
*   **Branch:** A safe, parallel copy of the project. You make your changes here so the original version stays perfectly safe. The default version everyone sees is called the `main` branch.
*   **Commit:** Saving your changes. It is like hitting "Save", but you must write a short note explaining what you changed.
*   **Pull Request (PR):** Asking your team or teacher to check your work. If they like it, they will "merge" (combine) your changes into the `main` branch.

---

## 📊 The GitHub Workflow Diagram

This is how data flows when you work on GitHub with your team:

```text
[ Main Branch: The Final Approved Project ]
         \
          \ (Step 1: Create a Branch)
           ▼
     [ Feature Branch: Your Safe Workspace ]
           │
           ▼ (Step 2: Commit - Save your edits)
     [ Saved Changes on your Branch ]
           │
           ▼ (Step 3: Pull Request - Ask for Review)
     [ Approved & Merged! Combines back into Main ]

# 💻 Module 2: Using the GitHub UI (Step-by-Step)

You do not need to download any special coding software to use GitHub. You can do everything directly on the GitHub website using the User Interface (UI).

---

## 🟦 Step 1: Create a Branch

Before making changes, always create a new branch so the original project stays safe.

1. Open your repository on GitHub.
2. Look at the top-left side and click the branch dropdown menu that says **main**.
3. Type a new branch name based on your assignment (for example: `student-notes`).
4. Click **Create branch: student-notes from 'main'**.

### Example

```text
main
  │
  └── student-notes
```

---

## 🟩 Step 2: Make Changes and Commit

Now you can safely add or edit files inside your new branch.

1. Make sure the branch selector shows **student-notes** (not **main**).
2. Click **Add file → Create new file**.
   - OR click the ✏️ pencil icon to edit an existing file.
3. Enter your content in the editor.
4. Click the **Commit changes...** button.

### Fill Out the Commit Form

**Commit Message:**

```text
Added Module 2 summary
```

**Where to Commit:**

✅ Select **Commit directly to the student-notes branch**

Click **Commit changes**.

### Commit Flow

```text
Edit File
    │
    ▼
Commit Changes
    │
    ▼
Saved to student-notes Branch
```

---

## 🟨 Step 3: Create a Pull Request (PR)

Once your changes are committed, create a Pull Request to request merging your work into the main branch.

1. Go back to the repository homepage.
2. GitHub may show a yellow notification bar:

```text
student-notes had recent pushes
```

3. Click **Compare & pull request**.
4. Enter a title and description explaining your changes.
5. Click **Create pull request**.

### Example PR

```text
Title:
Added Module 2 GitHub UI Notes

Description:
Created notes explaining branches,
commits, and pull requests.
```

---

# 📐 Visual Architecture Map

```text
+-------------------------------------------------------------+
|                     THE GITHUB UI WORKFLOW                  |
+-------------------------------------------------------------+
|                                                             |
|  1. MAIN BRANCH ---------> 2. CREATE BRANCH ('my-edits')    |
|     (Original Code)               |                         |
|            ▲                      | (Make changes online)   |
|            |                      ▼                         |
|            |               3. COMMIT CHANGES                |
|            |                      |                         |
|            |                      ▼                         |
|      5. MERGE PR <-------- 4. OPEN PULL REQUEST             |
|   (Changes Combined)       (Reviewing the work)             |
|                                                             |
+-------------------------------------------------------------+
```

---

# 🧠 Class 7 Summary & Checklist

- [x] Branches keep the main project safe while you experiment.
- [x] Commit messages explain what changes were made.
- [x] Pull Requests allow others to review your work before merging.
- [x] Changes are first saved in a branch and then merged into main.
- [x] GitHub UI allows file editing, commits, and PR creation directly from the browser.

---

# 🎯 Key Terms

| Term | Meaning |
|--------|---------|
| Branch | A separate workspace for making changes safely |
| Commit | A saved snapshot of your changes |
| Commit Message | A short description of what changed |
| Pull Request (PR) | A request to merge branch changes into main |
| Merge | Combining approved changes into the main branch |

---

## 🚀 GitHub UI Workflow

```text
Create Branch
      │
      ▼
Edit Files
      │
      ▼
Commit Changes
      │
      ▼
Create Pull Request
      │
      ▼
Review
      │
      ▼
Merge to Main
```     
