# 🟢 Lab 03 — GitHub Authentication


---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Configuration-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-02-configuration.md)
[![Beginner_Index](https://img.shields.io/badge/Beginner_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/First_Repo_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-04-first-repo.md)

</div>

---

<div align="center">

**Jump to section:**

[![Authentication_Methods_Overv](https://img.shields.io/badge/Authentication_Methods_Overv-F05032?style=flat-square&logo=git&logoColor=white)](#authentication-methods-overview)
[![Method_1_GitHub_CLI_Login_Re](https://img.shields.io/badge/Method_1_GitHub_CLI_Login_Re-F05032?style=flat-square&logo=git&logoColor=white)](#method-1-github-cli-login-recommended-for-beginners)
[![Method_2_Personal_Access_Tok](https://img.shields.io/badge/Method_2_Personal_Access_Tok-F05032?style=flat-square&logo=git&logoColor=white)](#method-2-personal-access-token-pat)
[![Method_3_SSH_Key_Setup](https://img.shields.io/badge/Method_3_SSH_Key_Setup-F05032?style=flat-square&logo=git&logoColor=white)](#method-3-ssh-key-setup)
[![Switching_Between_HTTPS_and_](https://img.shields.io/badge/Switching_Between_HTTPS_and_-F05032?style=flat-square&logo=git&logoColor=white)](#switching-between-https-and-ssh)
[![Managing_gh_CLI_Auth](https://img.shields.io/badge/Managing_gh_CLI_Auth-F05032?style=flat-square&logo=git&logoColor=white)](#managing-gh-cli-auth)
[![Lab_Task](https://img.shields.io/badge/Lab_Task-F05032?style=flat-square&logo=git&logoColor=white)](#lab-task)

</div>

---
> **Goal:** Authenticate with GitHub using both HTTPS tokens and the `gh` CLI.

---

## 🔐 Authentication Methods Overview

| Method | Best For | Security |
|--------|----------|----------|
| `gh auth login` | Daily CLI use | ✅ High (OAuth) |
| Personal Access Token (PAT) | Scripts, automation | ✅ High (scoped) |
| SSH Key | CI/CD, DevOps | ✅✅ Very High |
| GitHub App | Enterprise bots | ✅✅✅ Highest |

---

## 🛠️ Method 1: GitHub CLI Login (Recommended for Beginners)

```bash
gh auth login
```

You'll be prompted to choose:

```
? What account do you want to log into?
  ❯ GitHub.com
    GitHub Enterprise Server

? What is your preferred protocol for Git operations?
  ❯ HTTPS
    SSH

? Authenticate Git with your GitHub credentials? (Y/n) Y

? How would you like to authenticate GitHub CLI?
  ❯ Login with a web browser
    Paste an authentication token
```

**Select "Login with a web browser"**, then:
1. A code like `XXXX-XXXX` will appear
2. Press Enter — browser opens to `github.com/login/device`
3. Paste the code and authorize

```bash
# Verify login
gh auth status

# Expected output:
# github.com
#   ✓ Logged in to github.com as YourUsername (...)
#   ✓ Git operations protocol: https
#   ✓ Token scopes: gist, read:org, repo, workflow
```

---

## 🛠️ Method 2: Personal Access Token (PAT)

Use a PAT when you can't do browser auth (headless servers, scripts).

### Create a PAT

1. Go to **GitHub → Settings → Developer Settings → Personal Access Tokens → Tokens (classic)**
2. Click **"Generate new token"**
3. Select scopes:
   - `repo` — Full repo access
   - `workflow` — GitHub Actions
   - `read:org` — Org membership
4. Copy the token (you won't see it again!)

### Use the PAT

```bash
# Option A: Pass it during clone (one time)
git clone https://YOUR_TOKEN@github.com/username/repo.git

# Option B: Configure credential helper to store it
git config --global credential.helper store
git push   # You'll be prompted once; token is stored after

# Option C: Use gh CLI with PAT
gh auth login --with-token < token.txt
```

---

## 🛠️ Method 3: SSH Key Setup

SSH is the most secure option and avoids typing passwords.

```bash
# Step 1: Generate SSH key
ssh-keygen -t ed25519 -C "your@email.com"
# Press Enter for defaults, optionally set a passphrase

# Step 2: Start SSH agent
eval "$(ssh-agent -s)"
# Windows (Git Bash):
eval $(ssh-agent -s)

# Step 3: Add key to agent
ssh-add ~/.ssh/id_ed25519

# Step 4: Copy public key
cat ~/.ssh/id_ed25519.pub
# Windows:
type %USERPROFILE%\.ssh\id_ed25519.pub
```

### Add SSH Key to GitHub

```bash
# Option A: Copy manually
# Go to: GitHub → Settings → SSH and GPG Keys → New SSH Key
# Paste the public key content

# Option B: Use gh CLI (easiest!)
gh ssh-key add ~/.ssh/id_ed25519.pub --title "My Laptop"
```

```bash
# Step 5: Test SSH connection
ssh -T git@github.com
# Expected: Hi username! You've successfully authenticated...
```

---

## 🔄 Switching Between HTTPS and SSH

```bash
# Check current remote URL
git remote -v

# Switch from HTTPS to SSH
git remote set-url origin git@github.com:USERNAME/REPO.git

# Switch from SSH to HTTPS
git remote set-url origin https://github.com/USERNAME/REPO.git
```

---

## 🔑 Managing gh CLI Auth

```bash
# View current status
gh auth status

# Login to additional accounts
gh auth login --hostname github.com

# Logout
gh auth logout

# Refresh token scopes
gh auth refresh -s delete_repo

# View your token
gh auth token
```

---

## 🧪 Lab Task

```bash
# Task 1: Login with gh CLI
gh auth login

# Task 2: Verify auth status
gh auth status

# Task 3: View your GitHub profile via CLI
gh api user --jq '.login, .name, .public_repos'

# Task 4: List your repos
gh repo list --limit 5
```

✅ **Check:** You can see your username and repo list from the terminal.
---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Configuration-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-02-configuration.md)
[![Beginner_Index](https://img.shields.io/badge/Beginner_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/First_Repo_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-04-first-repo.md)

</div>