# 🟡 Lab 07 — Branching Strategies

> **Goal:** Create, switch, and manage branches — the core of team collaboration.

---

## 🌿 What is a Branch?

A branch is a **lightweight, movable pointer** to a commit. The default branch is `main`.

```
main:    A → B → C
                  ↘
feature:           D → E
```

Branches let multiple people work on different features simultaneously without interfering.

---

## 🔧 Branch Basics

```bash
# List all local branches
git branch

# List all branches (local + remote)
git branch -a

# List remote branches only
git branch -r

# Create a new branch
git branch feature-login

# Switch to a branch
git switch feature-login
# Old syntax: git checkout feature-login

# Create AND switch in one command (recommended)
git switch -c feature-login
# Old syntax: git checkout -b feature-login

# Switch back to main
git switch main

# Create branch from a specific commit
git switch -c hotfix-bug abc1234

# Create a branch from remote
git switch -c feature-login origin/feature-login
```

---

## 🏷️ Branch Naming Conventions

```bash
# Standard patterns used in professional teams:
feature/user-authentication
feature/JIRA-123-payment-gateway
fix/login-redirect-bug
hotfix/critical-sql-injection
chore/upgrade-dependencies
docs/update-api-readme
release/v2.1.0

# Examples:
git switch -c feature/user-authentication
git switch -c fix/broken-homepage-link
git switch -c hotfix/payment-failure
```

---

## 🔍 Inspecting Branches

```bash
# See last commit on each branch
git branch -v

# See which branches are merged into main
git branch --merged main

# See which branches are NOT merged
git branch --no-merged main

# Show branches with tracking info
git branch -vv

# Show when each branch was last touched
git for-each-ref --sort=-committerdate refs/heads/ \
  --format='%(refname:short) %(committerdate:relative)'
```

---

## 🗑️ Deleting Branches

```bash
# Delete a merged branch (safe)
git branch -d feature-login

# Force delete even if not merged (use with care!)
git branch -D feature-wip

# Delete a remote branch
git push origin --delete feature-login

# Delete a remote branch (alternate syntax)
git push origin :feature-login

# Prune deleted remote branches from local tracking
git fetch --prune
git remote prune origin
```

---

## 🌍 Popular Branching Strategies

### Strategy 1: GitHub Flow (Simple — Recommended for small teams)

```
main → feature-branch → Pull Request → merge to main → deploy
```

```bash
git switch main && git pull
git switch -c feature/new-dashboard
# ... make changes, commit ...
git push -u origin feature/new-dashboard
gh pr create --title "Add new dashboard" --body "Implements issue #42"
# After review → merge → delete branch
```

### Strategy 2: Git Flow (Complex — For release-based products)

```
main ← release branches ← develop ← feature branches
main ← hotfix branches
```

```bash
# Install git-flow
brew install git-flow  # macOS
apt install git-flow   # Linux

git flow init
git flow feature start user-profile
# ... work ...
git flow feature finish user-profile
git flow release start v1.2.0
git flow release finish v1.2.0
```

### Strategy 3: Trunk-Based Development (Advanced)

```
main ← short-lived feature branches (< 1 day)
Feature flags control releases
```

---

## 🧪 Lab Task

```bash
# Step 1: Create and switch to a feature branch
git switch main && git pull
git switch -c feature/add-about-page

# Step 2: Make changes on the branch
echo "<h1>About Us</h1>" > about.html
git add about.html
git commit -m "feat: add about page"

# Step 3: Create another change
echo "About page styles" > about.css
git add about.css
git commit -m "style: add about page CSS"

# Step 4: View branch log vs main
git log --oneline --graph main feature/add-about-page

# Step 5: Push the branch
git push -u origin feature/add-about-page

# Step 6: View branches
git branch -v
git branch --no-merged main
```

✅ **Check:** `git log --oneline --graph --all` shows your branch diverging from `main`.

---

➡️ **[Next: Lab 08 — Merge vs Rebase →](./lab-08-merging-rebasing.md)**
