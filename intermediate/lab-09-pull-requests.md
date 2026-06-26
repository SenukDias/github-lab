# 🟡 Lab 09 — Pull Requests & Code Review


---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Merging_%26_Rebasing-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-08-merging-rebasing.md)
[![Intermediate_Index](https://img.shields.io/badge/Intermediate_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Collaboration_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-10-collaboration.md)

</div>

---

<div align="center">

**Jump to section:**

[![What_is_a_Pull_Request](https://img.shields.io/badge/What_is_a_Pull_Request-F05032?style=flat-square&logo=git&logoColor=white)](#what-is-a-pull-request)
[![Creating_a_Pull_Request](https://img.shields.io/badge/Creating_a_Pull_Request-F05032?style=flat-square&logo=git&logoColor=white)](#creating-a-pull-request)
[![Reviewing_Pull_Requests](https://img.shields.io/badge/Reviewing_Pull_Requests-F05032?style=flat-square&logo=git&logoColor=white)](#reviewing-pull-requests)
[![Merging_Pull_Requests](https://img.shields.io/badge/Merging_Pull_Requests-F05032?style=flat-square&logo=git&logoColor=white)](#merging-pull-requests)
[![PR_Labels_Milestones_%26_Ass](https://img.shields.io/badge/PR_Labels_Milestones_%26_Ass-F05032?style=flat-square&logo=git&logoColor=white)](#pr-labels-milestones-assignments)
[![PR_Comments_%26_Discussions](https://img.shields.io/badge/PR_Comments_%26_Discussions-F05032?style=flat-square&logo=git&logoColor=white)](#pr-comments-discussions)
[![PR_Best_Practices](https://img.shields.io/badge/PR_Best_Practices-F05032?style=flat-square&logo=git&logoColor=white)](#pr-best-practices)
[![What_does_this_PR_do](https://img.shields.io/badge/What_does_this_PR_do-F05032?style=flat-square&logo=git&logoColor=white)](#what-does-this-pr-do)
[![How_to_test](https://img.shields.io/badge/How_to_test-F05032?style=flat-square&logo=git&logoColor=white)](#how-to-test)
[![Screenshots_if_UI_changes](https://img.shields.io/badge/Screenshots_if_UI_changes-F05032?style=flat-square&logo=git&logoColor=white)](#screenshots-if-ui-changes)
[![Checklist](https://img.shields.io/badge/Checklist-F05032?style=flat-square&logo=git&logoColor=white)](#checklist)
[![Branch_Protection_Rules_via_](https://img.shields.io/badge/Branch_Protection_Rules_via_-F05032?style=flat-square&logo=git&logoColor=white)](#branch-protection-rules-via-cli)
[![Lab_Task](https://img.shields.io/badge/Lab_Task-F05032?style=flat-square&logo=git&logoColor=white)](#lab-task)

</div>

---
> **Goal:** Open, review, and manage Pull Requests using both the web and `gh` CLI.

---

## 🔄 What is a Pull Request?

A **Pull Request (PR)** is a proposal to merge one branch into another. It's the core collaboration unit in GitHub:

```
feature/login  →  [Pull Request]  →  main
                       ↑
                  Code review
                  CI checks
                  Discussion
```

---

## 📤 Creating a Pull Request

### Via `gh` CLI

```bash
# Basic PR
gh pr create --title "feat: add user login" --body "Implements #42"

# Detailed PR with labels and reviewers
gh pr create \
  --title "feat: add user authentication" \
  --body "## What this PR does
  - Adds JWT-based login
  - Adds logout endpoint
  - Unit tests included

  Closes #42" \
  --reviewer teammate1,teammate2 \
  --label "feature,needs-review" \
  --base main

# Open in browser to fill in the PR form
gh pr create --web

# Draft PR (not ready for review)
gh pr create --title "WIP: new dashboard" --draft
```

### Via Web

1. Push your branch to GitHub
2. Go to your repo → you'll see a yellow banner "Compare & pull request"
3. Fill in title, description, reviewers, labels
4. Click **"Create pull request"**

---

## 🔍 Reviewing Pull Requests

```bash
# List open PRs
gh pr list

# View a specific PR
gh pr view 42
gh pr view 42 --web    # Open in browser

# Check out a PR locally for testing
gh pr checkout 42

# Review all changed files
gh pr diff 42
```

### Leaving Reviews via CLI

```bash
# Approve a PR
gh pr review 42 --approve

# Request changes
gh pr review 42 --request-changes --body "Please add error handling"

# Leave a comment
gh pr review 42 --comment --body "Looks good overall, minor nit below"
```

---

## ✅ Merging Pull Requests

```bash
# Merge (default — merge commit)
gh pr merge 42

# Squash merge (all commits become one)
gh pr merge 42 --squash

# Rebase merge (linear history)
gh pr merge 42 --rebase

# Merge and delete branch automatically
gh pr merge 42 --squash --delete-branch

# Merge when all checks pass (auto-merge)
gh pr merge 42 --auto
```

---

## 🏷️ PR Labels, Milestones & Assignments

```bash
# Add label to a PR
gh pr edit 42 --add-label "bug,high-priority"

# Remove label
gh pr edit 42 --remove-label "needs-review"

# Assign PR to someone
gh pr edit 42 --add-assignee username

# Set milestone
gh pr edit 42 --milestone "v2.0"

# Update PR title/body
gh pr edit 42 --title "fix: resolve auth token expiry"
```

---

## 💬 PR Comments & Discussions

```bash
# Comment on a PR
gh pr comment 42 --body "LGTM! 🚀"

# Reference a PR in a commit
git commit -m "fix: resolve issue, closes #42"
# Keywords: closes, fixes, resolves — auto-closes the issue on merge

# Link PRs together
gh pr edit 42 --body "Related to #38, depends on #41"
```

---

## 📋 PR Best Practices

```markdown
# Good PR Description Template:

## 🎯 What does this PR do?
A clear description of the changes.

## 🧪 How to test?
1. Run `npm test`
2. Visit `/login` and try logging in

## 📸 Screenshots (if UI changes)
[attach screenshots]

## ✅ Checklist
- [ ] Tests pass
- [ ] Documentation updated
- [ ] No breaking changes
```

---

## 🔒 Branch Protection Rules (via CLI)

```bash
# Require PR reviews before merging
gh api repos/{owner}/{repo}/branches/main/protection \
  --method PUT \
  --field required_status_checks=null \
  --field enforce_admins=false \
  --field required_pull_request_reviews='{"required_approving_review_count":1}' \
  --field restrictions=null
```

---

## 🧪 Lab Task

```bash
# Step 1: Push a feature branch to your repo
git switch -c feature/pr-practice
echo "# PR Practice" > pr-note.md
echo "Learning PR workflow" >> pr-note.md
git add pr-note.md
git commit -m "docs: add PR practice note"
git push -u origin feature/pr-practice

# Step 2: Create a PR via CLI
gh pr create \
  --title "docs: add PR practice note" \
  --body "## What this PR does
  Adds a practice note file to demonstrate the PR workflow.

  Part of the GitHub lab session." \
  --base main

# Step 3: View your PR
gh pr list
gh pr view --web

# Step 4: Merge your own PR
gh pr merge --squash --delete-branch

# Step 5: Update local main
git switch main && git pull
git log --oneline -5
```

✅ **Check:** `git log` on main shows your squash-merged commit.
---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Merging_%26_Rebasing-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-08-merging-rebasing.md)
[![Intermediate_Index](https://img.shields.io/badge/Intermediate_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Collaboration_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-10-collaboration.md)

</div>