# 🟢 Lab 01 — Installing Git & GitHub CLI

> **Goal:** Get Git and the `gh` CLI installed and verified on your machine.

---

## 🔍 What is Git vs GitHub?

| Git | GitHub |
|-----|--------|
| A **local tool** that tracks file changes | A **cloud platform** that hosts Git repos |
| Runs on your computer | Runs in the browser + has a REST API |
| Free, open-source | Free tier + paid plans |
| Created by Linus Torvalds (2005) | Acquired by Microsoft (2018) |

---

## 📦 Part 1: Install Git

### Windows

**Option A — Git for Windows (recommended for beginners)**
```bash
# Download from: https://git-scm.com/download/win
# Run the installer with default settings
# This also installs Git Bash — a Unix-like terminal
```

**Option B — Winget (Windows Package Manager)**
```powershell
winget install --id Git.Git -e --source winget
```

**Option C — Chocolatey**
```powershell
choco install git
```

### macOS

```bash
# Option A: Xcode Command Line Tools (built-in)
xcode-select --install

# Option B: Homebrew (recommended)
brew install git
```

### Linux (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install git -y
```

### Linux (Fedora/RHEL)

```bash
sudo dnf install git -y
```

---

## ✅ Verify Git Installation

```bash
git --version
# Expected output: git version 2.x.x
```

---

## 📦 Part 2: Install GitHub CLI (`gh`)

The `gh` CLI lets you interact with GitHub directly from your terminal — no browser needed.

### Windows

```powershell
# Option A: Winget
winget install --id GitHub.cli

# Option B: Chocolatey
choco install gh

# Option C: Scoop
scoop install gh
```

### macOS

```bash
brew install gh
```

### Linux (Ubuntu/Debian)

```bash
(type -p wget >/dev/null || (sudo apt update && sudo apt-get install wget -y)) \
&& sudo mkdir -p -m 755 /etc/apt/keyrings \
&& wget -qO- https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
&& sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
&& sudo apt update \
&& sudo apt install gh -y
```

### Linux (Fedora/RHEL)

```bash
sudo dnf install 'dnf-command(config-manager)'
sudo dnf config-manager --add-repo https://cli.github.com/packages/rpm/gh-cli.repo
sudo dnf install gh -y
```

---

## ✅ Verify `gh` Installation

```bash
gh --version
# Expected output: gh version 2.x.x (...)
```

---

## 🔍 Understanding the Tools

```bash
# Get help for any git command
git help commit
git commit --help

# Get help for any gh command
gh help
gh repo --help
```

---

## 🧪 Lab Task

Run the following and screenshot / note the output:

```bash
git --version
gh --version
which git      # Linux/macOS
where git      # Windows
```

Expected results:
- Git version ≥ 2.30
- gh version ≥ 2.0

---

## ❓ Troubleshooting

| Problem | Solution |
|---------|----------|
| `git: command not found` | Restart terminal after install |
| `gh: command not found` | Add gh to PATH, restart terminal |
| Windows: PATH not updated | Add `C:\Program Files\Git\bin` to System PATH |

---

➡️ **[Next: Lab 02 — Configuring Git →](./lab-02-configuration.md)**
