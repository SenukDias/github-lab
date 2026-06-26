# 🚀 GitHub & Docker Technical Lab Session

> **A hands-on, structured lab for learning GitHub and Docker from zero to pro.**

---

## 🎯 What You'll Learn

This lab is designed as a **progressive technical session** guiding you from installation to advanced workflows. Labs are divided into three tiers:

| Level | 🟢 Beginner | 🟡 Intermediate | 🔴 Pro |
|-------|------------|----------------|--------|
| Focus | Install, Configure, First Repo | Branching, PRs, Actions | Advanced Git, CI/CD, Security |
| Prerequisites | None | Beginner complete | Intermediate complete |
| Time | ~90 min | ~120 min | ~150 min |

---

## 📂 Lab Structure

```
github-docker-lab/
├── 🟢 beginner/
│   ├── lab-01-installation.md        → Install Git & GitHub CLI
│   ├── lab-02-configuration.md       → Configure Git identity
│   ├── lab-03-github-login.md        → Authenticate with GitHub
│   ├── lab-04-first-repo.md          → Create your first repository
│   ├── lab-05-basic-commands.md      → Essential Git commands
│   └── lab-06-gitignore.md           → Ignoring files properly
│
├── 🟡 intermediate/
│   ├── lab-07-branching.md           → Branching strategies
│   ├── lab-08-merging-rebasing.md    → Merge vs Rebase
│   ├── lab-09-pull-requests.md       → PRs and code review
│   ├── lab-10-collaboration.md       → Fork, clone, contribute
│   └── lab-11-github-actions.md      → Automate with Actions
│
├── 🔴 pro/
│   ├── lab-12-advanced-git.md        → Cherry-pick, bisect, reflog
│   ├── lab-13-github-cli-pro.md      → Master the gh CLI
│   ├── lab-14-git-hooks.md           → Automate with hooks
│   ├── lab-15-advanced-actions.md    → Full CI/CD pipelines
│   └── lab-16-security.md           → Secrets, protection rules
│
├── 📋 tasks/
│   ├── beginner-tasks.md
│   ├── intermediate-tasks.md
│   └── pro-tasks.md
│
└── .github/workflows/
    └── lab-validator.yml             → Auto-check your lab submissions
```

---

## 🛠️ Prerequisites

- A computer with internet access
- A [GitHub account](https://github.com/signup) (free)
- Willingness to type commands 💪

---

## 🚦 How to Use This Lab

1. **Start from Beginner** even if you have some experience — the foundation matters
2. **Run every command** yourself — reading is not enough
3. **Complete the tasks** at the end of each level before moving on
4. **Ask questions** — no such thing as a dumb question in a lab

---

## 🔗 Quick Navigation

- [🟢 Start Beginner Labs](./beginner/)
- [🟡 Start Intermediate Labs](./intermediate/)
- [🔴 Start Pro Labs](./pro/)
- [📋 All Tasks](./tasks/)

---

## 📌 Cheat Sheet Quick Reference

```bash
# --- Identity ---
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# --- Daily Workflow ---
git status          # What changed?
git add .           # Stage everything
git commit -m "msg" # Snapshot
git push            # Upload to GitHub
git pull            # Download from GitHub

# --- GitHub CLI ---
gh auth login       # Authenticate
gh repo create      # New repo
gh pr create        # Open a Pull Request
gh issue list       # View issues
```

---

*Made for the GitHub & Docker Technical Session — Learn by doing!*
