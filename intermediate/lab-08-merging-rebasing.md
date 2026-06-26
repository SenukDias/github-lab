# 🟡 Lab 08 — Merging & Rebasing


---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Branching-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-07-branching.md)
[![Intermediate_Index](https://img.shields.io/badge/Intermediate_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Pull_Requests_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-09-pull-requests.md)

</div>

---

<div align="center">

**Jump to section:**

[![Merge_vs_Rebase_The_Core_Dif](https://img.shields.io/badge/Merge_vs_Rebase_The_Core_Dif-F05032?style=flat-square&logo=git&logoColor=white)](#merge-vs-rebase-the-core-difference)
[![Merging](https://img.shields.io/badge/Merging-F05032?style=flat-square&logo=git&logoColor=white)](#merging)
[![Rebasing](https://img.shields.io/badge/Rebasing-F05032?style=flat-square&logo=git&logoColor=white)](#rebasing)
[![Resolving_Merge_Conflicts](https://img.shields.io/badge/Resolving_Merge_Conflicts-F05032?style=flat-square&logo=git&logoColor=white)](#resolving-merge-conflicts)
[![Squash_Merging_Clean_History](https://img.shields.io/badge/Squash_Merging_Clean_History-F05032?style=flat-square&logo=git&logoColor=white)](#squash-merging-clean-history)
[![Lab_Task](https://img.shields.io/badge/Lab_Task-F05032?style=flat-square&logo=git&logoColor=white)](#lab-task)

</div>

---
> **Goal:** Understand merge vs rebase and resolve conflicts like a pro.

---

## 🔀 Merge vs Rebase — The Core Difference

```
MERGE:                          REBASE:
main:    A─B─C                  main:    A─B─C
              ↘                                ↘
feature: D─E   M  ← merge       feature:       D'─E'  ← replayed on top
```

| | Merge | Rebase |
|--|-------|--------|
| History | Preserves all commits + merge commit | Creates linear history |
| Safe on shared branches? | ✅ Yes | ⚠️ Only for private branches |
| Use case | Finishing features, PR merges | Keeping feature branch up to date |

---

## 🔀 Merging

```bash
# Merge feature into main
git switch main
git merge feature/add-about-page

# Types of merges:

# Fast-forward merge (no new commit, just moves pointer)
# Happens when main has no new commits since branch was created
git merge feature/quick-fix

# True merge (creates a merge commit)
git merge --no-ff feature/user-auth
# --no-ff = "no fast-forward" — always creates a merge commit

# Merge with a custom message
git merge --no-ff feature/login -m "Merge feature/login: add JWT auth"

# Preview what would be merged (dry run)
git merge --no-commit --no-ff feature/login
git diff --staged   # See what would be committed
git merge --abort   # Cancel it
```

---

## 🔁 Rebasing

```bash
# Rebase your feature branch onto the latest main
git switch feature/add-about-page
git rebase main

# Interactive rebase — rewrite/squash last N commits
git rebase -i HEAD~3

# Interactive rebase options:
# pick   = keep as-is
# reword = change commit message
# edit   = pause and amend
# squash = meld into previous commit (keep message)
# fixup  = meld into previous commit (discard message)
# drop   = delete this commit

# Abort a rebase if things go wrong
git rebase --abort

# Continue after resolving conflicts
git rebase --continue

# Rebase onto main from remote
git fetch origin
git rebase origin/main
```

---

## ⚔️ Resolving Merge Conflicts

Conflicts happen when two branches change the **same line** of a file.

```bash
# Trigger a conflict (example)
git switch main
echo "Version 1.0" > version.txt
git commit -am "chore: set version to 1.0"

git switch feature/update-version
echo "Version 2.0" > version.txt
git commit -am "chore: set version to 2.0"

git switch main
git merge feature/update-version
# CONFLICT (content): Merge conflict in version.txt
```

### Conflict Markers

```
<<<<<<< HEAD
Version 1.0       ← your version (main)
=======
Version 2.0       ← their version (feature)
>>>>>>> feature/update-version
```

### Resolving the Conflict

```bash
# Option 1: Manually edit the file
# Remove the markers, keep what you want:
# Version 2.0

# Option 2: Use git's merge tool
git mergetool   # Opens configured merge tool (VS Code, vimdiff, etc.)

# Option 3: Accept one side entirely
git checkout --ours version.txt    # Keep main's version
git checkout --theirs version.txt  # Keep feature's version

# After resolving:
git add version.txt
git commit -m "merge: resolve version conflict, use 2.0"
```

### Setting Up a Visual Merge Tool

```bash
# VS Code
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'

# Using VS Code built-in conflict editor
# Just open the file in VS Code — it shows Accept Current/Incoming buttons
```

---

## 📦 Squash Merging (Clean History)

```bash
# Squash all commits from branch into one
git switch main
git merge --squash feature/messy-branch
git commit -m "feat: implement user authentication"
# Result: one clean commit on main instead of 10 messy ones
```

---

## 🧪 Lab Task

```bash
# Setup: Create two conflicting branches
mkdir merge-lab && cd merge-lab
git init
echo "Hello World" > message.txt
git add . && git commit -m "init: add message"

# Branch 1
git switch -c branch-a
echo "Hello from Branch A" > message.txt
git commit -am "feat: branch A message"

# Branch 2
git switch main
git switch -c branch-b
echo "Hello from Branch B" > message.txt
git commit -am "feat: branch B message"

# Attempt to merge both
git switch main
git merge branch-a      # This works fine
git merge branch-b      # CONFLICT!

# Resolve the conflict
# Edit message.txt to say: "Hello from Branch A and B"
# Then:
git add message.txt
git commit -m "merge: combine messages from branch-a and branch-b"

# View the result
git log --oneline --graph --all
```

✅ **Check:** Log shows a merge commit with both branch histories visible.
---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Branching-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-07-branching.md)
[![Intermediate_Index](https://img.shields.io/badge/Intermediate_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Pull_Requests_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-09-pull-requests.md)

</div>