# 🟢 Lab 05 — Essential Git Commands


---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_First_Repo-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-04-first-repo.md)
[![Beginner_Index](https://img.shields.io/badge/Beginner_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/.gitignore_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-06-gitignore.md)

</div>

---

<div align="center">

**Jump to section:**

[![The_Git_Lifecycle](https://img.shields.io/badge/The_Git_Lifecycle-F05032?style=flat-square&logo=git&logoColor=white)](#the-git-lifecycle)
[![Checking_Status](https://img.shields.io/badge/Checking_Status-F05032?style=flat-square&logo=git&logoColor=white)](#checking-status)
[![Staging_Files](https://img.shields.io/badge/Staging_Files-F05032?style=flat-square&logo=git&logoColor=white)](#staging-files)
[![Committing](https://img.shields.io/badge/Committing-F05032?style=flat-square&logo=git&logoColor=white)](#committing)
[![Pushing_%26_Pulling](https://img.shields.io/badge/Pushing_%26_Pulling-F05032?style=flat-square&logo=git&logoColor=white)](#pushing-pulling)
[![Viewing_History](https://img.shields.io/badge/Viewing_History-F05032?style=flat-square&logo=git&logoColor=white)](#viewing-history)
[![Undoing_Things](https://img.shields.io/badge/Undoing_Things-F05032?style=flat-square&logo=git&logoColor=white)](#undoing-things)
[![Deleting_Files](https://img.shields.io/badge/Deleting_Files-F05032?style=flat-square&logo=git&logoColor=white)](#deleting-files)
[![Stashing_Temporary_Save](https://img.shields.io/badge/Stashing_Temporary_Save-F05032?style=flat-square&logo=git&logoColor=white)](#stashing-temporary-save)
[![Lab_Task](https://img.shields.io/badge/Lab_Task-F05032?style=flat-square&logo=git&logoColor=white)](#lab-task)

</div>

---
> **Goal:** Master the core Git workflow — the commands you'll use every single day.

---

## 🔄 The Git Lifecycle

```
Working Directory  →  Staging Area (Index)  →  Local Repo  →  Remote Repo
     (edit)              (git add)             (git commit)    (git push)
```

Files go through these states:
- **Untracked** — New file, Git doesn't know about it
- **Staged** — Marked to be included in the next commit
- **Committed** — Saved to local Git history
- **Pushed** — Uploaded to GitHub

---

## 📊 Checking Status

```bash
# See what's changed
git status

# Short version (cleaner)
git status -s
# M  = modified in staging
#  M = modified in working dir
# ?? = untracked
# A  = newly added

# Show changes in detail
git diff               # Working dir vs staging
git diff --staged      # Staging vs last commit
git diff HEAD          # Working dir vs last commit
```

---

## ➕ Staging Files

```bash
# Stage a specific file
git add README.md

# Stage multiple files
git add file1.py file2.py

# Stage everything in current directory
git add .

# Stage all tracked modified files (no new files)
git add -u

# Stage interactively (pick hunks)
git add -p
# You'll see each change and choose: y (yes), n (no), s (split)

# Unstage a file (keep changes in working dir)
git restore --staged README.md
# Old syntax: git reset HEAD README.md
```

---

## 💾 Committing

```bash
# Commit with inline message
git commit -m "feat: add user login page"

# Stage all tracked files AND commit in one step
git commit -am "fix: correct typo in README"

# Open editor for multi-line commit message
git commit

# Amend the LAST commit (change message or add forgotten files)
git add forgotten-file.py
git commit --amend -m "feat: add user login page with validation"
# ⚠️ Only amend commits NOT yet pushed to shared branches!
```

### ✍️ Good Commit Message Format

Use the **Conventional Commits** standard:
```
<type>(<scope>): <short description>

[optional body]
[optional footer]
```

Types:
- `feat` — New feature
- `fix` — Bug fix
- `docs` — Documentation only
- `style` — Formatting, no logic change
- `refactor` — Code restructure
- `test` — Adding tests
- `chore` — Maintenance tasks

```bash
# Good examples:
git commit -m "feat(auth): add JWT token validation"
git commit -m "fix(api): handle null response from user endpoint"
git commit -m "docs: update README with installation steps"
git commit -m "chore: upgrade axios to 1.6.0"
```

---

## 📤 Pushing & Pulling

```bash
# Push to remote
git push                          # Push current branch
git push origin main              # Push main to origin
git push -u origin feature-login  # Push + set upstream

# Force push (DANGEROUS — rewrites remote history)
git push --force-with-lease       # Safer version
git push -f                       # Dangerous version

# Pull (fetch + merge)
git pull
git pull origin main

# Pull with rebase (cleaner history)
git pull --rebase

# Fetch only (download but don't merge)
git fetch
git fetch origin
git fetch --all
```

---

## 📜 Viewing History

```bash
# View commit log
git log

# One line per commit
git log --oneline

# With branch graph
git log --oneline --graph --all

# Last 5 commits
git log -5

# Commits by author
git log --author="Jane"

# Commits touching a file
git log --follow -- src/app.py

# See what changed in a commit
git show abc1234
git show HEAD             # Latest commit
git show HEAD~1           # One before latest
```

---

## ↩️ Undoing Things

```bash
# Discard changes in working directory (irreversible!)
git restore README.md
# Old syntax: git checkout -- README.md

# Unstage a file
git restore --staged README.md

# Undo last commit, keep changes staged
git reset --soft HEAD~1

# Undo last commit, keep changes unstaged
git reset --mixed HEAD~1

# Undo last commit, discard all changes (DANGEROUS)
git reset --hard HEAD~1

# Create a new "undo" commit (safe for shared branches)
git revert abc1234
git revert HEAD
```

---

## 🗑️ Deleting Files

```bash
# Delete file AND tell Git about it
git rm old-file.txt

# Delete from Git tracking but keep the file locally
git rm --cached secret.env

# Remove a directory
git rm -r old-folder/
```

---

## 🏷️ Stashing (Temporary Save)

```bash
# Save work in progress without committing
git stash

# Stash with a message
git stash push -m "WIP: login form half-done"

# List stashes
git stash list

# Apply most recent stash (keeps it in stash)
git stash apply

# Apply AND remove from stash
git stash pop

# Apply a specific stash
git stash apply stash@{2}

# Drop a stash
git stash drop stash@{0}

# Clear all stashes
git stash clear
```

---

## 🧪 Lab Task

```bash
# Step 1: Create some files
mkdir practice-lab && cd practice-lab
git init

echo "# Practice Lab" > README.md
echo "print('hello')" > app.py
echo "*.log" > .gitignore

# Step 2: Check status
git status

# Step 3: Stage and commit
git add README.md app.py .gitignore
git status
git commit -m "feat: initial project setup"

# Step 4: Make changes
echo "print('world')" >> app.py
git diff

# Step 5: Stage partially, commit
git add app.py
git commit -m "feat: add world print"

# Step 6: View history
git log --oneline

# Step 7: Undo last commit but keep changes
git reset --soft HEAD~1
git status

# Step 8: Re-commit
git commit -m "feat: add world print to app"
git log --oneline
```

✅ **Check:** Your log shows 2 commits with meaningful messages.
---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_First_Repo-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-04-first-repo.md)
[![Beginner_Index](https://img.shields.io/badge/Beginner_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/.gitignore_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-06-gitignore.md)

</div>