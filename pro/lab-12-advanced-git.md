# 🔴 Lab 12 — Advanced Git Techniques


---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_GitHub_Actions-F05032?style=for-the-badge&logo=git&logoColor=white)](../intermediate/lab-11-github-actions.md)
[![Pro_Index](https://img.shields.io/badge/Pro_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/GitHub_CLI_Pro_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-13-github-cli-pro.md)

</div>

---

<div align="center">

**Jump to section:**

[![Cherry-Pick](https://img.shields.io/badge/Cherry-Pick-F05032?style=flat-square&logo=git&logoColor=white)](#cherry-pick)
[![Git_Bisect_Binary_Search_for](https://img.shields.io/badge/Git_Bisect_Binary_Search_for-F05032?style=flat-square&logo=git&logoColor=white)](#git-bisect-binary-search-for-bugs)
[![Git_Reflog_Your_Time_Machine](https://img.shields.io/badge/Git_Reflog_Your_Time_Machine-F05032?style=flat-square&logo=git&logoColor=white)](#git-reflog-your-time-machine)
[![Git_Worktrees_Multiple_Branc](https://img.shields.io/badge/Git_Worktrees_Multiple_Branc-F05032?style=flat-square&logo=git&logoColor=white)](#git-worktrees-multiple-branches-one-repo)
[![Interactive_Rebase_Mastery](https://img.shields.io/badge/Interactive_Rebase_Mastery-F05032?style=flat-square&logo=git&logoColor=white)](#interactive-rebase-mastery)
[![Git_Submodules](https://img.shields.io/badge/Git_Submodules-F05032?style=flat-square&logo=git&logoColor=white)](#git-submodules)
[![Signing_Commits_with_GPG](https://img.shields.io/badge/Signing_Commits_with_GPG-F05032?style=flat-square&logo=git&logoColor=white)](#signing-commits-with-gpg)
[![Lab_Task](https://img.shields.io/badge/Lab_Task-F05032?style=flat-square&logo=git&logoColor=white)](#lab-task)

</div>

---
> **Goal:** Master Git's power tools — cherry-pick, bisect, reflog, worktrees, and more.

---

## 🍒 Cherry-Pick

Pick a specific commit from any branch and apply it to your current branch.

```bash
# Basic cherry-pick
git cherry-pick abc1234

# Cherry-pick multiple commits
git cherry-pick abc1234 def5678

# Cherry-pick a range of commits
git cherry-pick abc1234^..def5678

# Cherry-pick without committing (stage only)
git cherry-pick --no-commit abc1234
git cherry-pick -n abc1234

# Cherry-pick and edit the commit message
git cherry-pick -e abc1234

# Abort if conflict
git cherry-pick --abort

# Continue after resolving conflict
git cherry-pick --continue

# Use case: backport a fix from main to a release branch
git switch release/v1.5
git cherry-pick $(git log main --oneline --grep="fix: critical bug" | awk '{print $1}')
```

---

## 🔍 Git Bisect (Binary Search for Bugs)

Find exactly which commit introduced a bug by binary search:

```bash
# Start bisect
git bisect start

# Mark current state as bad (has the bug)
git bisect bad

# Mark a known good commit (before the bug existed)
git bisect good v1.2.0
git bisect good abc1234

# Git checks out a commit — test it, then tell git:
git bisect bad    # this commit has the bug
git bisect good   # this commit is fine

# Git narrows down. After N steps, it finds the culprit.
# Result: "abc1234 is the first bad commit"

# View the commit that caused the bug
git show abc1234

# End bisect session
git bisect reset

# AUTOMATED bisect with a test script
git bisect start
git bisect bad HEAD
git bisect good v1.0.0
git bisect run npm test    # Run automatically until bad commit found
git bisect reset
```

---

## 🕰️ Git Reflog (Your Time Machine)

Reflog records every change to HEAD — even deleted commits and branches:

```bash
# View reflog
git reflog

# Output:
# abc1234 HEAD@{0}: commit: feat: new feature
# def5678 HEAD@{1}: reset: moving to HEAD~1
# ghi9012 HEAD@{2}: commit: initial commit

# Recover a deleted commit
git checkout def5678        # Go back to that state
git switch -c recovery-branch   # Save it as a branch

# Recover a deleted branch
git reflog --all | grep "deleted-branch-name"
git switch -c deleted-branch HEAD@{3}

# Undo a hard reset
git reset --hard HEAD~3     # Oops!
git reflog                  # Find the commit before the reset
git reset --hard HEAD@{1}   # Go back

# Set expiry (default is 90 days for unreachable commits)
git reflog expire --expire=30.days --expire-unreachable=now --all
```

---

## 🌳 Git Worktrees (Multiple Branches, One Repo)

Check out multiple branches simultaneously in different folders:

```bash
# Check out a branch as a worktree in a new folder
git worktree add ../hotfix-review hotfix/critical-bug

# Create a new branch in a worktree
git worktree add -b feature/parallel-work ../feature-work main

# List all worktrees
git worktree list

# Remove a worktree (after you're done)
git worktree remove ../hotfix-review
git worktree prune   # Clean up stale references

# Use case: Review a PR without stashing your work
git worktree add ../pr-42-review origin/feature/user-auth
cd ../pr-42-review && npm test
cd - && git worktree remove ../pr-42-review
```

---

## 🔁 Interactive Rebase Mastery

```bash
# Rewrite the last 5 commits
git rebase -i HEAD~5

# In the editor, example:
# pick a1b2c3 feat: add login
# pick d4e5f6 wip: debugging
# pick g7h8i9 fix: typo
# pick j1k2l3 fix: another typo
# pick m4n5o6 feat: add logout

# Rewrite to:
# pick a1b2c3 feat: add login
# fixup d4e5f6 wip: debugging      ← squash into previous, drop message
# fixup g7h8i9 fix: typo           ← squash typo fixes
# fixup j1k2l3 fix: another typo
# pick m4n5o6 feat: add logout     ← keep as separate commit

# Result: 2 clean commits instead of 5 messy ones

# Reorder commits
# pick m4n5o6 feat: add logout     ← move this UP
# pick a1b2c3 feat: add login

# Drop a commit entirely
# drop d4e5f6 wip: debugging
```

---

## 📦 Git Submodules

Include another repo as a subdirectory:

```bash
# Add a submodule
git submodule add https://github.com/org/shared-lib.git libs/shared

# Clone a repo with submodules
git clone --recurse-submodules https://github.com/org/project.git

# Update submodules after cloning without --recurse
git submodule update --init --recursive

# Update submodule to latest
git submodule update --remote libs/shared

# Remove a submodule
git submodule deinit libs/shared
git rm libs/shared
```

---

## 🗝️ Signing Commits with GPG

```bash
# Generate GPG key
gpg --gen-key

# List keys
gpg --list-secret-keys --keyid-format=long

# Copy key ID (the part after 4096R/ or ed25519/)
# Example: 3AA5C34371567BD2

# Configure git to sign commits
git config --global user.signingkey 3AA5C34371567BD2
git config --global commit.gpgsign true

# Sign a single commit
git commit -S -m "feat: signed commit"

# Add GPG key to GitHub
gpg --armor --export 3AA5C34371567BD2
# Copy output → GitHub Settings → SSH and GPG keys → New GPG key
```

---

## 🧪 Lab Task

```bash
# Setup
git log --oneline -10

# Task 1: Cherry-pick a specific commit to a new branch
COMMIT=$(git log --oneline -3 | tail -1 | awk '{print $1}')
git switch -c cherry-pick-demo
git cherry-pick $COMMIT

# Task 2: Use reflog to find and recover something
git log --oneline
git reset --hard HEAD~1   # "accidentally" delete last commit
git reflog
LOST=$(git reflog | head -2 | tail -1 | awk '{print $1}')
git reset --hard $LOST    # Recover it!
git log --oneline

# Task 3: Use worktrees to review a branch without stashing
git worktree add ../review-branch HEAD~1
git worktree list
ls ../review-branch/
git worktree remove ../review-branch
```

✅ **Check:** You recovered your commit using `reflog`.
---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_GitHub_Actions-F05032?style=for-the-badge&logo=git&logoColor=white)](../intermediate/lab-11-github-actions.md)
[![Pro_Index](https://img.shields.io/badge/Pro_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/GitHub_CLI_Pro_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-13-github-cli-pro.md)

</div>