# 📋 Intermediate Tasks


---

<div align="center">

[![← Beginner Tasks](https://img.shields.io/badge/←_Beginner_Tasks-e05030?style=for-the-badge&logo=git&logoColor=white)](beginner-tasks.md)
[![All Tasks](https://img.shields.io/badge/All_Tasks-F05032?style=for-the-badge&logo=git&logoColor=white)](../README.md#-all-labs)
[![Pro Tasks →](https://img.shields.io/badge/Pro_Tasks_→-e05030?style=for-the-badge&logo=git&logoColor=white)](pro-tasks.md)

</div>

---

<div align="center">

**Jump to section:**

[![Task_1_Feature_Branch_Workfl](https://img.shields.io/badge/Task_1_Feature_Branch_Workfl-F05032?style=flat-square&logo=git&logoColor=white)](#task-1-feature-branch-workflow)
[![Task_2_Conflict_Resolution_C](https://img.shields.io/badge/Task_2_Conflict_Resolution_C-F05032?style=flat-square&logo=git&logoColor=white)](#task-2-conflict-resolution-challenge)
[![Task_3_GitHub_Actions_CI](https://img.shields.io/badge/Task_3_GitHub_Actions_CI-F05032?style=flat-square&logo=git&logoColor=white)](#task-3-github-actions-ci)
[![Task_4_Issue-Driven_Developm](https://img.shields.io/badge/Task_4_Issue-Driven_Developm-F05032?style=flat-square&logo=git&logoColor=white)](#task-4-issue-driven-development)
[![Getting_Started](https://img.shields.io/badge/Getting_Started-F05032?style=flat-square&logo=git&logoColor=white)](#getting-started)
[![Commit_Convention](https://img.shields.io/badge/Commit_Convention-F05032?style=flat-square&logo=git&logoColor=white)](#commit-convention)
[![Task_5_Fork_and_Upstream_Syn](https://img.shields.io/badge/Task_5_Fork_and_Upstream_Syn-F05032?style=flat-square&logo=git&logoColor=white)](#task-5-fork-and-upstream-sync-challenge)
[![Submission](https://img.shields.io/badge/Submission-F05032?style=flat-square&logo=git&logoColor=white)](#submission)

</div>

---

These tasks simulate real team collaboration scenarios.

---

## Task 1 — Feature Branch Workflow

Simulate a 3-feature development sprint:

```bash
# Start from main
git switch main && git pull

# Feature 1: Add a homepage
git switch -c feature/homepage
echo "<h1>Welcome</h1>" > index.html
git add index.html
git commit -m "feat: add homepage"
git push -u origin feature/homepage
gh pr create --title "feat: add homepage" --body "Adds the main landing page"

# Feature 2: Add an API endpoint
git switch main && git pull
git switch -c feature/api-users
echo '{"users": []}' > api/users.json
mkdir -p api
echo '{"users": []}' > api/users.json
git add api/
git commit -m "feat: add users API endpoint"
git push -u origin feature/api-users
gh pr create --title "feat: add users API" --body "REST endpoint for user data"

# Merge both PRs and verify
gh pr list
gh pr merge feature/homepage --squash --delete-branch
gh pr merge feature/api-users --squash --delete-branch

git switch main && git pull
git log --oneline -5
```

---

## Task 2 — Conflict Resolution Challenge

```bash
# Setup: Create intentional conflict
mkdir conflict-lab && cd conflict-lab
git init
echo "shared line" > shared.txt
git add . && git commit -m "init: shared file"

# Branch A changes the file
git switch -c branch-a
echo "Branch A version" > shared.txt
git commit -am "feat: branch A changes"

# Branch B also changes the file
git switch main
git switch -c branch-b
echo "Branch B version" > shared.txt
git commit -am "feat: branch B changes"

# Merge A into main (works fine)
git switch main
git merge branch-a

# Merge B into main (CONFLICT!)
git merge branch-b

# TASK: Resolve the conflict so the file says:
# "Branch A and Branch B versions combined"
# Then commit the resolution
```

---

## Task 3 — GitHub Actions CI

Create a workflow that:

1. Triggers on every push and PR to `main`
2. Runs on `ubuntu-latest`
3. Has 3 separate jobs: `lint`, `test`, `report`
4. `lint` job: checks that all `.md` files exist in beginner/, intermediate/, pro/
5. `test` job: needs `lint` to pass first (use `needs:`)
6. `report` job: prints a summary to the workflow step summary

```yaml
# .github/workflows/intermediate-task.yml
# YOUR IMPLEMENTATION HERE
```

---

## Task 4 — Issue-Driven Development

Practice the full issue → branch → PR → merge cycle:

```bash
# Step 1: Create an issue
gh issue create \
  --title "Add contributing guide" \
  --body "We need a CONTRIBUTING.md with guidelines for new contributors" \
  --label "documentation"

# Step 2: View the issue number
gh issue list

# Step 3: Create a branch linked to the issue
ISSUE_NUM=$(gh issue list --json number --jq '.[0].number')
git switch -c "docs/contributing-guide-$ISSUE_NUM"

# Step 4: Create the file
cat > CONTRIBUTING.md << 'EOF'
# Contributing Guide

## Getting Started
1. Fork the repository
2. Create a feature branch: `git switch -c feature/your-feature`
3. Commit your changes: `git commit -m 'feat: add feature'`
4. Push to your fork: `git push origin feature/your-feature`
5. Open a Pull Request

## Commit Convention
Use Conventional Commits: feat|fix|docs|chore(<scope>): description
EOF

git add CONTRIBUTING.md
git commit -m "docs: add contributing guide, closes #$ISSUE_NUM"
git push -u origin "docs/contributing-guide-$ISSUE_NUM"

# Step 5: Open a PR that references the issue
gh pr create \
  --title "docs: add CONTRIBUTING.md" \
  --body "Closes #$ISSUE_NUM" \
  --base main

# Step 6: Merge and verify the issue closes automatically
gh pr merge --squash --delete-branch
gh issue view $ISSUE_NUM  # Should show as closed
```

---

## Task 5 — Fork and Upstream Sync Challenge

1. Fork a classmate's `my-github-lab` repo
2. Clone your fork
3. Add the original as `upstream`
4. Create a branch `contribution/YOUR_NAME`
5. Add a file `contributions/YOUR_NAME.txt` with a fun fact about yourself
6. Push and open a PR to their repo
7. After they merge, sync your fork with: `git fetch upstream && git rebase upstream/main`

---

## 🏆 Submission

```bash
echo "=== Intermediate Lab Submission ==="
gh pr list --state merged --limit 5 --json title,mergedAt \
  --jq '.[] | "\(.mergedAt[:10]) \(.title)"'
echo ""
echo "Open Issues:"
gh issue list --state open --limit 5
echo ""
echo "Workflow runs:"
gh run list --limit 5
```
---

<div align="center">

[![← Beginner Tasks](https://img.shields.io/badge/←_Beginner_Tasks-e05030?style=for-the-badge&logo=git&logoColor=white)](beginner-tasks.md)
[![All Tasks](https://img.shields.io/badge/All_Tasks-F05032?style=for-the-badge&logo=git&logoColor=white)](../README.md#-all-labs)
[![Pro Tasks →](https://img.shields.io/badge/Pro_Tasks_→-e05030?style=for-the-badge&logo=git&logoColor=white)](pro-tasks.md)

</div>