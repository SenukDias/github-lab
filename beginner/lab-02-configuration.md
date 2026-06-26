# 🟢 Lab 02 — Configuring Git Identity


---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Installation-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-01-installation.md)
[![Beginner_Index](https://img.shields.io/badge/Beginner_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/GitHub_Login_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-03-github-login.md)

</div>

---

<div align="center">

**Jump to section:**

[![Why_Configure_Git](https://img.shields.io/badge/Why_Configure_Git-F05032?style=flat-square&logo=git&logoColor=white)](#why-configure-git)
[![Part_1_Set_Global_Identity](https://img.shields.io/badge/Part_1_Set_Global_Identity-F05032?style=flat-square&logo=git&logoColor=white)](#part-1-set-global-identity)
[![Part_2_Set_Default_Branch_Na](https://img.shields.io/badge/Part_2_Set_Default_Branch_Na-F05032?style=flat-square&logo=git&logoColor=white)](#part-2-set-default-branch-name)
[![Part_3_Set_Your_Default_Edit](https://img.shields.io/badge/Part_3_Set_Your_Default_Edit-F05032?style=flat-square&logo=git&logoColor=white)](#part-3-set-your-default-editor)
[![Part_4_Useful_Config_Tweaks](https://img.shields.io/badge/Part_4_Useful_Config_Tweaks-F05032?style=flat-square&logo=git&logoColor=white)](#part-4-useful-config-tweaks)
[![View_Your_Configuration](https://img.shields.io/badge/View_Your_Configuration-F05032?style=flat-square&logo=git&logoColor=white)](#view-your-configuration)
[![Where_is_the_Config_File](https://img.shields.io/badge/Where_is_the_Config_File-F05032?style=flat-square&logo=git&logoColor=white)](#where-is-the-config-file)
[![Local_vs_Global_Config](https://img.shields.io/badge/Local_vs_Global_Config-F05032?style=flat-square&logo=git&logoColor=white)](#local-vs-global-config)
[![Lab_Task](https://img.shields.io/badge/Lab_Task-F05032?style=flat-square&logo=git&logoColor=white)](#lab-task)

</div>

---
> **Goal:** Set up your Git identity so every commit is properly attributed to you.

---

## 🧠 Why Configure Git?

Every commit you make is **stamped with your name and email**. This is how GitHub knows who made what change — it's your digital signature on your work.

---

## ⚙️ Part 1: Set Global Identity

These settings apply to **all** repositories on your machine.

```bash
# Set your name
git config --global user.name "Your Full Name"

# Set your email (use the same email as your GitHub account!)
git config --global user.email "you@example.com"
```

> ⚠️ Use the email linked to your GitHub account for commits to show up on your profile graph.

---

## ⚙️ Part 2: Set Default Branch Name

GitHub uses `main` as the default branch. Let's align Git to match:

```bash
git config --global init.defaultBranch main
```

---

## ⚙️ Part 3: Set Your Default Editor

Git opens a text editor for commit messages. Set it to your preference:

```bash
# VS Code (recommended)
git config --global core.editor "code --wait"

# Nano (simple terminal editor)
git config --global core.editor "nano"

# Vim
git config --global core.editor "vim"

# Notepad++ (Windows)
git config --global core.editor "'C:/Program Files/Notepad++/notepad++.exe' -multiInst -notabbar -nosession -noPlugin"
```

---

## ⚙️ Part 4: Useful Config Tweaks

```bash
# Colorize git output (usually on by default)
git config --global color.ui auto

# Set pull to rebase by default (cleaner history)
git config --global pull.rebase false

# Cache credentials for 1 hour (3600 seconds)
git config --global credential.helper cache
git config --global credential.helper 'cache --timeout=3600'

# Windows: credential manager (recommended on Windows)
git config --global credential.helper manager

# Auto-detect line endings (important for cross-platform teams)
# Windows:
git config --global core.autocrlf true
# macOS/Linux:
git config --global core.autocrlf input

# Show short status output
git config --global status.short true
```

---

## 🔍 View Your Configuration

```bash
# See all global config
git config --global --list

# See a specific setting
git config --global user.name
git config --global user.email

# See where config is stored
git config --global --list --show-origin
```

---

## 📁 Where is the Config File?

```bash
# The global config file lives at:
# Windows:  C:\Users\YourName\.gitconfig
# macOS:    ~/.gitconfig
# Linux:    ~/.gitconfig

# You can edit it directly:
cat ~/.gitconfig
```

Example `.gitconfig` file:
```ini
[user]
    name = Jane Doe
    email = jane@example.com
[core]
    editor = code --wait
    autocrlf = input
[init]
    defaultBranch = main
[color]
    ui = auto
[pull]
    rebase = false
```

---

## 📂 Local vs Global Config

| Scope | Command | Applies To |
|-------|---------|------------|
| Global | `git config --global` | All repos on your machine |
| Local | `git config --local` | Only the current repo |
| System | `git config --system` | All users on the machine |

```bash
# Override email for ONE specific repo (e.g., work project)
cd my-work-repo
git config --local user.email "jane@company.com"
```

---

## 🧪 Lab Task

```bash
# Step 1: Set your identity
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global init.defaultBranch main
git config --global core.editor "code --wait"

# Step 2: Verify
git config --global --list

# Step 3: Find the config file
cat ~/.gitconfig    # macOS/Linux
type %USERPROFILE%\.gitconfig  # Windows CMD
```

✅ **Check:** Your name and email appear in the output.
---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Installation-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-01-installation.md)
[![Beginner_Index](https://img.shields.io/badge/Beginner_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/GitHub_Login_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-03-github-login.md)

</div>