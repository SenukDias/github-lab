# 🟡 Lab 11 — GitHub Actions (CI/CD Introduction)


---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Collaboration-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-10-collaboration.md)
[![Intermediate_Index](https://img.shields.io/badge/Intermediate_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Advanced_Git_→-F05032?style=for-the-badge&logo=git&logoColor=white)](../pro/lab-12-advanced-git.md)

</div>

---

<div align="center">

**Jump to section:**

[![What_is_GitHub_Actions](https://img.shields.io/badge/What_is_GitHub_Actions-F05032?style=flat-square&logo=git&logoColor=white)](#what-is-github-actions)
[![Workflow_File_Location](https://img.shields.io/badge/Workflow_File_Location-F05032?style=flat-square&logo=git&logoColor=white)](#workflow-file-location)
[![Your_First_Workflow](https://img.shields.io/badge/Your_First_Workflow-F05032?style=flat-square&logo=git&logoColor=white)](#your-first-workflow)
[![CI_Workflow_Run_Tests](https://img.shields.io/badge/CI_Workflow_Run_Tests-F05032?style=flat-square&logo=git&logoColor=white)](#ci-workflow-run-tests)
[![Using_Secrets_in_Workflows](https://img.shields.io/badge/Using_Secrets_in_Workflows-F05032?style=flat-square&logo=git&logoColor=white)](#using-secrets-in-workflows)
[![Workflow_Triggers](https://img.shields.io/badge/Workflow_Triggers-F05032?style=flat-square&logo=git&logoColor=white)](#workflow-triggers)
[![Managing_Workflows_via_CLI](https://img.shields.io/badge/Managing_Workflows_via_CLI-F05032?style=flat-square&logo=git&logoColor=white)](#managing-workflows-via-cli)
[![Lab_Task](https://img.shields.io/badge/Lab_Task-F05032?style=flat-square&logo=git&logoColor=white)](#lab-task)
[![Intermediate_Track_Complete](https://img.shields.io/badge/Intermediate_Track_Complete-F05032?style=flat-square&logo=git&logoColor=white)](#intermediate-track-complete)

</div>

---
> **Goal:** Automate testing, linting, and deployment with GitHub Actions workflows.

---

## ⚙️ What is GitHub Actions?

GitHub Actions is a **CI/CD platform** built into GitHub. It lets you automate tasks triggered by repository events.

```
Push code → Workflow triggers → Run tests → Build → Deploy
```

### Core Concepts

| Term | Meaning |
|------|---------|
| **Workflow** | A YAML file that defines automation |
| **Event** | What triggers the workflow (push, PR, schedule) |
| **Job** | A set of steps that run on one machine |
| **Step** | A single command or action |
| **Action** | A reusable unit (like a plugin) |
| **Runner** | The machine that runs jobs (GitHub-hosted or self-hosted) |

---

## 📂 Workflow File Location

All workflows go in `.github/workflows/`:

```
.github/
  workflows/
    ci.yml          ← runs tests on every push
    deploy.yml      ← deploys on push to main
    nightly.yml     ← runs every night at midnight
```

---

## 🏗️ Your First Workflow

Create `.github/workflows/hello.yml`:

```yaml
name: Hello World

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  greet:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Say Hello
        run: echo "Hello from GitHub Actions! 👋"

      - name: Show repository info
        run: |
          echo "Repo: ${{ github.repository }}"
          echo "Branch: ${{ github.ref_name }}"
          echo "Commit: ${{ github.sha }}"
          echo "Actor: ${{ github.actor }}"
```

---

## 🧪 CI Workflow — Run Tests

Create `.github/workflows/ci.yml`:

```yaml
name: CI — Run Tests

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12"]

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}

      - name: Install dependencies
        run: |
          pip install --upgrade pip
          pip install -r requirements.txt

      - name: Run linter
        run: |
          pip install flake8
          flake8 . --max-line-length=120

      - name: Run tests
        run: |
          pip install pytest
          pytest tests/ -v
```

### Node.js Version

```yaml
name: CI — Node.js

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [18, 20, 22]

    steps:
      - uses: actions/checkout@v4

      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'

      - run: npm ci
      - run: npm run build --if-present
      - run: npm test
```

---

## 🔑 Using Secrets in Workflows

```yaml
# In workflow YAML:
steps:
  - name: Deploy to server
    run: ./deploy.sh
    env:
      API_KEY: ${{ secrets.API_KEY }}
      DB_PASSWORD: ${{ secrets.DATABASE_PASSWORD }}
```

```bash
# Add secrets via gh CLI
gh secret set API_KEY
gh secret set DATABASE_PASSWORD --body "super-secret"

# List secrets (names only, values are masked)
gh secret list

# Delete a secret
gh secret delete API_KEY
```

---

## 🔄 Workflow Triggers

```yaml
on:
  # Manual trigger
  workflow_dispatch:
    inputs:
      environment:
        description: 'Target environment'
        required: true
        default: 'staging'
        type: choice
        options: [staging, production]

  # Schedule (cron)
  schedule:
    - cron: '0 2 * * *'   # Every day at 2 AM UTC

  # On tag push
  push:
    tags: ['v*.*.*']

  # On release published
  release:
    types: [published]

  # On specific file changes
  push:
    paths:
      - 'src/**'
      - 'package.json'
```

---

## 📋 Managing Workflows via CLI

```bash
# List workflow runs
gh run list

# View a specific run
gh run view 1234567

# Watch a running workflow
gh run watch 1234567

# Re-run a failed workflow
gh run rerun 1234567

# Re-run only failed jobs
gh run rerun 1234567 --failed-only

# Download workflow artifacts
gh run download 1234567

# Manually trigger a workflow (workflow_dispatch)
gh workflow run ci.yml
gh workflow run deploy.yml --field environment=staging

# List all workflows
gh workflow list

# Enable/disable a workflow
gh workflow enable ci.yml
gh workflow disable nightly.yml
```

---

## 🧪 Lab Task

```bash
# Step 1: In your lab repo, create the workflows directory
mkdir -p .github/workflows

# Step 2: Create a simple CI workflow
cat > .github/workflows/ci.yml << 'EOF'
name: Lab CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  lint-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Validate structure
        run: |
          echo "✅ Repo structure:"
          ls -la
          echo "✅ All checks passed!"

      - name: Print lab info
        run: |
          echo "Repository: ${{ github.repository }}"
          echo "Triggered by: ${{ github.actor }}"
          echo "Branch: ${{ github.ref_name }}"
EOF

# Step 3: Commit and push
git add .github/
git commit -m "ci: add basic lab CI workflow"
git push

# Step 4: Watch it run
gh run list
gh run watch   # watch latest run
```

✅ **Check:** `gh run list` shows a ✅ green check next to your latest push.

---

## 🏁 Intermediate Track Complete!

You now know how to:
- ✅ Create and manage branches
- ✅ Merge and rebase with conflict resolution
- ✅ Open, review and merge Pull Requests
- ✅ Fork repos and contribute upstream
- ✅ Write GitHub Actions workflows
---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Collaboration-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-10-collaboration.md)
[![Intermediate_Index](https://img.shields.io/badge/Intermediate_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Advanced_Git_→-F05032?style=for-the-badge&logo=git&logoColor=white)](../pro/lab-12-advanced-git.md)

</div>