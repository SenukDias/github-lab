# 🟢 Lab 04 — Creating Your First Repository


---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_GitHub_Login-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-03-github-login.md)
[![Beginner_Index](https://img.shields.io/badge/Beginner_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Basic_Commands_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-05-basic-commands.md)

</div>

---

<div align="center">

**Jump to section:**

[![What_is_a_Repository](https://img.shields.io/badge/What_is_a_Repository-F05032?style=flat-square&logo=git&logoColor=white)](#what-is-a-repository)
[![Method_1_Create_via_GitHub_W](https://img.shields.io/badge/Method_1_Create_via_GitHub_W-F05032?style=flat-square&logo=git&logoColor=white)](#method-1-create-via-github-website)
[![Method_2_Create_via_gh_CLI_R](https://img.shields.io/badge/Method_2_Create_via_gh_CLI_R-F05032?style=flat-square&logo=git&logoColor=white)](#method-2-create-via-gh-cli-recommended)
[![Cloning_a_Repository](https://img.shields.io/badge/Cloning_a_Repository-F05032?style=flat-square&logo=git&logoColor=white)](#cloning-a-repository)
[![Initialize_a_Local_Repo](https://img.shields.io/badge/Initialize_a_Local_Repo-F05032?style=flat-square&logo=git&logoColor=white)](#initialize-a-local-repo)
[![Connecting_Local_to_Remote](https://img.shields.io/badge/Connecting_Local_to_Remote-F05032?style=flat-square&logo=git&logoColor=white)](#connecting-local-to-remote)
[![Your_First_Push](https://img.shields.io/badge/Your_First_Push-F05032?style=flat-square&logo=git&logoColor=white)](#your-first-push)
[![Useful_Repository_Commands](https://img.shields.io/badge/Useful_Repository_Commands-F05032?style=flat-square&logo=git&logoColor=white)](#useful-repository-commands)
[![Lab_Task](https://img.shields.io/badge/Lab_Task-F05032?style=flat-square&logo=git&logoColor=white)](#lab-task)

</div>

---
> **Goal:** Create a GitHub repository using both the web UI and the `gh` CLI, then connect it locally.

---

## 📖 What is a Repository?

A **repository** (repo) is a folder tracked by Git. It stores:
- All your project files
- The complete history of every change ever made
- Branches, tags, and metadata

```
my-project/
├── .git/          ← Git's internal database (DON'T TOUCH)
├── README.md
├── src/
└── tests/
```

---

## 🌐 Method 1: Create via GitHub Website

1. Go to **github.com** and click the **+** icon → **New repository**
2. Fill in:
   - **Repository name:** `my-first-repo`
   - **Description:** `Learning Git basics`
   - **Visibility:** Public
   - ✅ Check **"Add a README file"**
3. Click **"Create repository"**

---

## 💻 Method 2: Create via `gh` CLI (Recommended!)

```bash
# Interactive creation
gh repo create

# Non-interactive — public repo with README
gh repo create my-first-repo --public --description "Learning Git basics" --add-readme

# Private repo
gh repo create my-secret-project --private

# Create AND clone locally in one step
gh repo create my-first-repo --public --clone

# Create from an existing local folder
cd my-local-project
gh repo create my-local-project --public --source=. --push
```

---

## 📥 Cloning a Repository

```bash
# Clone via HTTPS
git clone https://github.com/USERNAME/my-first-repo.git

# Clone via SSH
git clone git@github.com:USERNAME/my-first-repo.git

# Clone via gh CLI (handles auth automatically)
gh repo clone USERNAME/my-first-repo

# Clone into a specific folder name
git clone https://github.com/USERNAME/repo.git my-folder-name

# Clone only the latest snapshot (faster, no full history)
git clone --depth 1 https://github.com/USERNAME/repo.git
```

---

## 📁 Initialize a Local Repo

```bash
# Create a new folder and initialize Git
mkdir my-project && cd my-project
git init

# You'll see:
# Initialized empty Git repository in .../my-project/.git/

# Check git status
git status
# On branch main
# No commits yet
# nothing to commit
```

---

## 🔗 Connecting Local to Remote

```bash
# Add a remote (named "origin" by convention)
git remote add origin https://github.com/USERNAME/my-first-repo.git

# Verify remotes
git remote -v
# origin  https://github.com/USERNAME/my-first-repo.git (fetch)
# origin  https://github.com/USERNAME/my-first-repo.git (push)

# Rename a remote
git remote rename origin upstream

# Remove a remote
git remote remove upstream
```

---

## 📤 Your First Push

```bash
# Create a file
echo "# My First Repo" > README.md

# Stage it
git add README.md

# Commit it
git commit -m "Initial commit: add README"

# Push to GitHub (first time)
git push -u origin main
# The -u sets upstream tracking — next time just: git push
```

---

## 🔍 Useful Repository Commands

```bash
# View repo info via gh
gh repo view
gh repo view USERNAME/repo --web    # Open in browser

# List all repos for a user
gh repo list USERNAME

# View repo details as JSON
gh repo view --json name,description,url,stargazerCount

# Clone any public repo
gh repo clone torvalds/linux
```

---

## 🧪 Lab Task

```bash
# Step 1: Create a new repo called "git-lab-practice"
gh repo create git-lab-practice --public --description "My Git learning lab" --clone

# Step 2: Enter the folder
cd git-lab-practice

# Step 3: Create some files
echo "# Git Lab Practice" > README.md
echo "print('Hello GitHub!')" > hello.py
echo "node_modules/" > .gitignore

# Step 4: Add, commit, push
git add .
git commit -m "feat: initial lab setup with README and hello script"
git push

# Step 5: View it on GitHub
gh repo view --web
```

✅ **Check:** Your repo appears on github.com with all 3 files.
---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_GitHub_Login-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-03-github-login.md)
[![Beginner_Index](https://img.shields.io/badge/Beginner_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Basic_Commands_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-05-basic-commands.md)

</div>