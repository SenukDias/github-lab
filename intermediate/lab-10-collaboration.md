# 🟡 Lab 10 — Collaboration & Forking

> **Goal:** Fork repos, sync with upstream, and work effectively in teams.

---

## 🍴 Fork vs Clone

| | Fork | Clone |
|--|------|-------|
| Creates a copy on | **Your GitHub account** | **Your local machine** |
| Connected to | Original (upstream) | Your remote (origin) |
| Use case | Contributing to others' repos | Working on your own repos |

```
Original Repo (upstream)
        ↓ fork
Your GitHub Copy (origin)
        ↓ clone
Your Local Machine
```

---

## 🍴 Forking a Repository

```bash
# Fork via gh CLI
gh repo fork torvalds/linux --clone

# Fork and clone in one step
gh repo fork microsoft/vscode --clone --remote

# Fork without cloning
gh repo fork expressjs/express

# List your forks
gh repo list --fork
```

---

## 🔄 Staying in Sync with Upstream

```bash
# Add the original repo as "upstream"
git remote add upstream https://github.com/ORIGINAL_OWNER/REPO.git

# Verify remotes
git remote -v
# origin    https://github.com/YOU/REPO.git (fetch)
# origin    https://github.com/YOU/REPO.git (push)
# upstream  https://github.com/ORIGINAL/REPO.git (fetch)
# upstream  https://github.com/ORIGINAL/REPO.git (push)

# Fetch updates from upstream
git fetch upstream

# Merge upstream changes into your main
git switch main
git merge upstream/main

# Or rebase instead of merge (cleaner)
git rebase upstream/main

# Push your updated main to your fork
git push origin main
```

---

## 👥 Team Collaboration Workflow

```bash
# Scenario: Working on a team project

# Step 1: Clone the shared repo
gh repo clone org/project

# Step 2: Always pull before starting work
git switch main
git pull

# Step 3: Create a feature branch
git switch -c feature/your-initials-feature-name

# Step 4: Work, commit often
git add .
git commit -m "feat: implement pagination"
git push -u origin feature/your-initials-feature-name

# Step 5: Open a PR
gh pr create --title "feat: add pagination to user list"

# Step 6: After PR is merged, clean up
git switch main
git pull
git branch -d feature/your-initials-feature-name
git remote prune origin
```

---

## 👀 Watching & Starring Repos

```bash
# Star a repo
gh repo view torvalds/linux --json stargazerCount
# (Starring via CLI requires API call)
gh api user/starred/torvalds/linux -X PUT

# Watch a repo for notifications
gh api repos/microsoft/vscode/subscription -X PUT \
  --field subscribed=true \
  --field ignored=false

# List your starred repos
gh api user/starred --jq '.[].full_name'
```

---

## 🔔 GitHub Issues in Collaboration

```bash
# Create an issue
gh issue create --title "Bug: login fails on Safari" \
  --body "Steps to reproduce: 1. Open Safari 2. Go to /login 3. See error" \
  --label "bug"

# List issues
gh issue list
gh issue list --label "bug" --state open

# View an issue
gh issue view 5

# Assign yourself
gh issue edit 5 --add-assignee @me

# Close an issue
gh issue close 5 --comment "Fixed in PR #12"

# Reference in commits — auto-closes on merge
git commit -m "fix: resolve Safari login issue, fixes #5"
```

---

## 🌐 GitHub Gists (Quick Code Sharing)

```bash
# Create a public gist
gh gist create script.py --public --desc "Useful Python utility"

# Create from multiple files
gh gist create file1.py file2.py

# Create a private gist
gh gist create secret.sh --desc "Private script"

# List your gists
gh gist list

# View a gist
gh gist view GIST_ID

# Clone a gist
gh gist clone GIST_ID
```

---

## 🧪 Lab Task

```bash
# Step 1: Fork the lab repo (your instructor's repo)
gh repo fork INSTRUCTOR_USERNAME/github-docker-lab --clone --remote

# Step 2: Add upstream
git remote add upstream https://github.com/INSTRUCTOR_USERNAME/github-docker-lab.git
git remote -v

# Step 3: Create a contribution branch
git switch -c contribution/YOUR_NAME-lab-notes

# Step 4: Add a file with your notes
mkdir contributions
echo "# Notes by YOUR_NAME
- I learned about forks and upstream
- The remote -v command is very useful
" > contributions/YOUR_NAME.md

git add contributions/
git commit -m "docs: add lab notes by YOUR_NAME"
git push -u origin contribution/YOUR_NAME-lab-notes

# Step 5: Open a PR to the upstream repo
gh pr create \
  --repo INSTRUCTOR_USERNAME/github-docker-lab \
  --title "docs: add lab notes by YOUR_NAME" \
  --body "Contributing my notes from Lab 10"

# Step 6: Sync your fork with upstream
git fetch upstream
git switch main
git rebase upstream/main
git push
```

✅ **Check:** A PR appears on the upstream repo from your fork.

---

➡️ **[Next: Lab 11 — GitHub Actions →](./lab-11-github-actions.md)**
