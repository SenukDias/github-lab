<div align="center">

<img src="./assets/banner.svg" alt="GitHub & Docker Lab Session" width="100%" />

<br/>

[![Labs](https://img.shields.io/badge/Labs-16_total-0d1117?style=flat-square&logo=github&logoColor=white&labelColor=21262d&color=238636)](./beginner/)
[![Beginner](https://img.shields.io/badge/Beginner-6_labs-238636?style=flat-square&labelColor=1a3020)](./beginner/)
[![Intermediate](https://img.shields.io/badge/Intermediate-5_labs-9a6700?style=flat-square&labelColor=201a10)](./intermediate/)
[![Pro](https://img.shields.io/badge/Pro-5_labs-b91c1c?style=flat-square&labelColor=201010)](./pro/)
[![CI](https://img.shields.io/github/actions/workflow/status/SenukDias/github-docker-lab/lab-validator.yml?style=flat-square&label=Lab+Validator&logo=githubactions&logoColor=white)](https://github.com/SenukDias/github-docker-lab/actions)

</div>

---

## 📖 Theory Notes — Read Before You Start

> Understanding the **why** makes the **how** stick. Work through these visual notes before opening the first lab.

<table>
<tr>
<td width="50%">

### 01 — What is Git & GitHub?

<a href="./assets/theory-01-git-github.svg">
<img src="./assets/theory-01-git-github.svg" alt="Theory 01 – Git vs GitHub" width="100%"/>
</a>

> Git is the tool · GitHub is the service

</td>
<td width="50%">

### 02 — The Git Lifecycle

<a href="./assets/theory-02-lifecycle.svg">
<img src="./assets/theory-02-lifecycle.svg" alt="Theory 02 – Git Lifecycle" width="100%"/>
</a>

> Working Dir → Staging → Local → Remote

</td>
</tr>
<tr>
<td width="50%">

### 03 — Branching

<a href="./assets/theory-03-branching.svg">
<img src="./assets/theory-03-branching.svg" alt="Theory 03 – Branching" width="100%"/>
</a>

> Parallel work without interference

</td>
<td width="50%">

### 04 — GitHub Flow

<a href="./assets/theory-04-github-flow.svg">
<img src="./assets/theory-04-github-flow.svg" alt="Theory 04 – GitHub Flow" width="100%"/>
</a>

> 6-step cycle: branch → PR → merge

</td>
</tr>
</table>

---

## 🚀 Choose Your Starting Level

<p align="center">
  <a href="./beginner/">
    <img src="./assets/btn-beginner.svg" alt="Start Beginner Labs" width="240"/>
  </a>
  &nbsp;&nbsp;
  <a href="./intermediate/">
    <img src="./assets/btn-intermediate.svg" alt="Start Intermediate Labs" width="240"/>
  </a>
  &nbsp;&nbsp;
  <a href="./pro/">
    <img src="./assets/btn-pro.svg" alt="Start Pro Labs" width="240"/>
  </a>
</p>

---

## 📂 Full Lab Structure

```
github-docker-lab/
│
├── 🟢 beginner/                      ← Start here if you are new
│   ├── lab-01-installation.md        Install Git & GitHub CLI (all OS)
│   ├── lab-02-configuration.md       Configure identity, editor, line endings
│   ├── lab-03-github-login.md        HTTPS, PAT, SSH — 3 auth methods
│   ├── lab-04-first-repo.md          Create repos via web + gh CLI
│   ├── lab-05-basic-commands.md      add · commit · push · pull · log · stash
│   └── lab-06-gitignore.md           Patterns, templates, removing tracked files
│
├── 🟡 intermediate/
│   ├── lab-07-branching.md           GitHub Flow, Git Flow, naming conventions
│   ├── lab-08-merging-rebasing.md    Merge vs Rebase, conflict resolution
│   ├── lab-09-pull-requests.md       Create, review, merge PRs via gh CLI
│   ├── lab-10-collaboration.md       Fork, upstream sync, issues, gists
│   └── lab-11-github-actions.md      Write CI workflows, triggers, secrets
│
├── 🔴 pro/
│   ├── lab-12-advanced-git.md        cherry-pick, bisect, reflog, worktrees
│   ├── lab-13-github-cli-pro.md      gh api, releases, scripting, extensions
│   ├── lab-14-git-hooks.md           pre-commit, commit-msg, pre-push, Husky
│   ├── lab-15-advanced-actions.md    Multi-job CI/CD, Docker build, environments
│   └── lab-16-security.md           Branch protection, CODEOWNERS, secret scanning
│
├── 📋 tasks/
│   ├── beginner-tasks.md             5 practical challenges + submission script
│   ├── intermediate-tasks.md         5 team simulation tasks
│   └── pro-tasks.md                  5 real-world engineering challenges
│
└── .github/workflows/
    └── lab-validator.yml             Auto-validates lab structure on every push
```

---

## ⚡ Quick Cheat Sheet

<details>
<summary><strong>🟢 Beginner Commands</strong></summary>

```bash
# Setup
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
gh auth login

# Daily workflow
git status                  # What changed?
git add .                   # Stage all
git add -p                  # Stage interactively (best practice)
git commit -m "feat: msg"   # Commit
git push                    # Upload to GitHub
git pull                    # Download from GitHub

# History & undo
git log --oneline --graph
git diff
git restore --staged file   # Unstage
git reset --soft HEAD~1     # Undo last commit (keep changes)
```
</details>

<details>
<summary><strong>🟡 Intermediate Commands</strong></summary>

```bash
# Branches
git switch -c feature/name          # Create + switch
git switch main && git pull         # Always pull before branching
git branch --no-merged main         # List unmerged branches
git branch -d feature/done          # Delete merged branch

# Pull Requests
gh pr create --title "feat: ..." --body "Closes #42"
gh pr list
gh pr merge --squash --delete-branch
gh pr review 42 --approve

# Collaboration
gh repo fork owner/repo --clone --remote
git remote add upstream URL
git fetch upstream && git rebase upstream/main
```
</details>

<details>
<summary><strong>🔴 Pro Commands</strong></summary>

```bash
# Advanced Git
git cherry-pick abc1234             # Apply specific commit
git bisect start && git bisect bad  # Find bug commit
git reflog                          # Recover anything
git worktree add ../hotfix HEAD     # Multiple checkouts

# Interactive rebase
git rebase -i HEAD~5                # Squash/reword/reorder

# gh CLI scripting
gh api user --jq '.login'
gh release create v1.0.0 --generate-notes
gh run list --workflow ci.yml --status failure
gh secret set API_KEY
```
</details>

---

## 🛠️ Prerequisites

| Requirement | Details |
|-------------|---------|
| Computer | Windows / macOS / Linux |
| Internet | Required for GitHub |
| GitHub account | [Sign up free →](https://github.com/signup) |
| Terminal | Git Bash (Windows), Terminal (macOS/Linux) |
| Text editor | VS Code recommended |

---

## 🎯 How to Use This Lab

1. **Read the theory cards above** — understand the concepts visually
2. **Start at Beginner** even with some experience — foundation matters
3. **Run every command yourself** — reading is not learning
4. **Complete the tasks** before moving to the next level
5. **Ask questions** — there are no dumb questions in a lab

---

<div align="center">

*Made for the GitHub & Docker Technical Session · Learn by doing · Star ⭐ if this helped you*

</div>
