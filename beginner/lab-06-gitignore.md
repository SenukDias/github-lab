# 🟢 Lab 06 — The .gitignore File


---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Basic_Commands-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-05-basic-commands.md)
[![Beginner_Index](https://img.shields.io/badge/Beginner_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Branching_→-F05032?style=for-the-badge&logo=git&logoColor=white)](../intermediate/lab-07-branching.md)

</div>

---

<div align="center">

**Jump to section:**

[![Why_Do_We_Need_.gitignore](https://img.shields.io/badge/Why_Do_We_Need_.gitignore-F05032?style=flat-square&logo=git&logoColor=white)](#why-do-we-need-gitignore)
[![Basic_.gitignore_Syntax](https://img.shields.io/badge/Basic_.gitignore_Syntax-F05032?style=flat-square&logo=git&logoColor=white)](#basic-gitignore-syntax)
[![Quick_Start_Generate_a_.giti](https://img.shields.io/badge/Quick_Start_Generate_a_.giti-F05032?style=flat-square&logo=git&logoColor=white)](#quick-start-generate-a-gitignore)
[![Common_.gitignore_Templates](https://img.shields.io/badge/Common_.gitignore_Templates-F05032?style=flat-square&logo=git&logoColor=white)](#common-gitignore-templates)
[![Managing_Ignored_Files](https://img.shields.io/badge/Managing_Ignored_Files-F05032?style=flat-square&logo=git&logoColor=white)](#managing-ignored-files)
[![Removing_Already-Tracked_Fil](https://img.shields.io/badge/Removing_Already-Tracked_Fil-F05032?style=flat-square&logo=git&logoColor=white)](#removing-already-tracked-files)
[![Types_of_.gitignore_Files](https://img.shields.io/badge/Types_of_.gitignore_Files-F05032?style=flat-square&logo=git&logoColor=white)](#types-of-gitignore-files)
[![Lab_Task](https://img.shields.io/badge/Lab_Task-F05032?style=flat-square&logo=git&logoColor=white)](#lab-task)
[![Beginner_Track_Complete](https://img.shields.io/badge/Beginner_Track_Complete-F05032?style=flat-square&logo=git&logoColor=white)](#beginner-track-complete)

</div>

---
> **Goal:** Learn to control which files Git tracks — and which it ignores forever.

---

## 🤔 Why Do We Need .gitignore?

Not everything in your project folder should be committed to Git:

- 🔑 **Secrets** — API keys, passwords, `.env` files
- 📦 **Dependencies** — `node_modules/`, `venv/`, `vendor/`
- 🏗️ **Build outputs** — `dist/`, `build/`, `*.class`, `*.pyc`
- 💻 **Editor files** — `.vscode/`, `.idea/`, `*.swp`
- 🖥️ **OS files** — `.DS_Store` (Mac), `Thumbs.db` (Windows)

---

## 📝 Basic .gitignore Syntax

```gitignore
# This is a comment

# Ignore a specific file
secret.env
config/database.yml

# Ignore all .log files
*.log

# Ignore all .tmp files anywhere
**/*.tmp

# Ignore a folder
node_modules/
build/
dist/

# Ignore all files in a folder
logs/*

# Negate a pattern (DO track this even if parent is ignored)
!logs/important.log

# Ignore files only in the root
/config.local.json

# Ignore files in any "temp" folder
temp/

# Ignore files ending in .env except .env.example
*.env
!.env.example
```

---

## 🏃 Quick Start: Generate a .gitignore

```bash
# Via GitHub's gitignore API
curl -L https://www.toptal.com/developers/gitignore/api/node > .gitignore
curl -L https://www.toptal.com/developers/gitignore/api/python > .gitignore

# Or use gh CLI to create a repo with a template .gitignore
gh repo create my-project --public --gitignore Node

# Multiple templates
curl "https://www.toptal.com/developers/gitignore/api/python,django,vscode" > .gitignore
```

---

## 📋 Common .gitignore Templates

### Node.js / JavaScript

```gitignore
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*
.npm
dist/
build/
.env
.env.local
.env.*.local
coverage/
*.log
.DS_Store
```

### Python

```gitignore
__pycache__/
*.py[cod]
*$py.class
*.so
.env
.venv
venv/
ENV/
env/
dist/
build/
*.egg-info/
.pytest_cache/
.mypy_cache/
.coverage
htmlcov/
*.log
.DS_Store
```

### Java / Maven

```gitignore
target/
*.class
*.jar
*.war
*.ear
.classpath
.project
.settings/
.idea/
*.iml
```

---

## 🔧 Managing Ignored Files

```bash
# Check if a file is ignored
git check-ignore -v node_modules
git check-ignore -v secret.env

# See all ignored files
git status --ignored

# Force add an ignored file (override .gitignore)
git add -f config.override.json

# List what .gitignore rules apply to a path
git check-ignore -v --no-index path/to/file
```

---

## 🚨 Removing Already-Tracked Files

If you accidentally committed a file that should be ignored:

```bash
# Step 1: Add the pattern to .gitignore
echo "secret.env" >> .gitignore

# Step 2: Remove from Git tracking (keeps file locally)
git rm --cached secret.env

# For a whole folder
git rm -r --cached node_modules/

# Step 3: Commit the removal
git commit -m "chore: stop tracking secret.env"

# Step 4: Push
git push
```

> ⚠️ The file still exists in Git history! For sensitive data, use `git filter-branch` or [BFG Repo Cleaner](https://rtyley.github.io/bfg-repo-cleaner/).

---

## 📁 Types of .gitignore Files

| File | Scope | Committed? |
|------|-------|------------|
| `.gitignore` | Current repo | ✅ Yes |
| `~/.gitignore_global` | All repos on machine | No (personal) |
| `.git/info/exclude` | Current repo only | No (local secret) |

```bash
# Set up a global gitignore for personal editor files
git config --global core.excludesfile ~/.gitignore_global

# Add to it
echo ".DS_Store" >> ~/.gitignore_global
echo ".vscode/" >> ~/.gitignore_global
echo "Thumbs.db" >> ~/.gitignore_global
```

---

## 🧪 Lab Task

```bash
# Step 1: Create a project structure
mkdir gitignore-lab && cd gitignore-lab
git init

# Step 2: Create files (some should be ignored)
echo "console.log('app')" > app.js
mkdir node_modules && echo "fake module" > node_modules/lodash.js
echo "API_KEY=super-secret-123" > .env
echo "APP_NAME=MyApp" > .env.example
echo "app log" > debug.log
echo "# My App" > README.md

# Step 3: Create .gitignore
cat > .gitignore << 'EOF'
node_modules/
*.log
.env
!.env.example
EOF

# Step 4: Check what's tracked
git status
# Should show: app.js, .env.example, README.md, .gitignore
# Should NOT show: node_modules/, debug.log, .env

# Step 5: Verify ignored files
git check-ignore -v node_modules
git check-ignore -v .env

# Step 6: Add, commit
git add .
git commit -m "chore: set up project with .gitignore"
git status
```

✅ **Check:** `node_modules/`, `.env`, and `debug.log` do NOT appear in `git status`.

---

## 🏁 Beginner Track Complete!

You now know how to:
- ✅ Install Git and gh CLI
- ✅ Configure your identity
- ✅ Authenticate with GitHub
- ✅ Create and clone repos
- ✅ Add, commit, push, pull
- ✅ Write a proper .gitignore
---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Basic_Commands-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-05-basic-commands.md)
[![Beginner_Index](https://img.shields.io/badge/Beginner_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Branching_→-F05032?style=for-the-badge&logo=git&logoColor=white)](../intermediate/lab-07-branching.md)

</div>