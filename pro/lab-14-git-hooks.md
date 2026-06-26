# 🔴 Lab 14 — Git Hooks


---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_GitHub_CLI_Pro-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-13-github-cli-pro.md)
[![Pro_Index](https://img.shields.io/badge/Pro_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Advanced_Actions_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-15-advanced-actions.md)

</div>

---

<div align="center">

**Jump to section:**

[![What_are_Git_Hooks](https://img.shields.io/badge/What_are_Git_Hooks-F05032?style=flat-square&logo=git&logoColor=white)](#what-are-git-hooks)
[![Hook_Locations](https://img.shields.io/badge/Hook_Locations-F05032?style=flat-square&logo=git&logoColor=white)](#hook-locations)
[![pre-commit_Hook_Run_Before_E](https://img.shields.io/badge/pre-commit_Hook_Run_Before_E-F05032?style=flat-square&logo=git&logoColor=white)](#pre-commit-hook-run-before-every-commit)
[![commit-msg_Hook_Validate_Com](https://img.shields.io/badge/commit-msg_Hook_Validate_Com-F05032?style=flat-square&logo=git&logoColor=white)](#commit-msg-hook-validate-commit-format)
[![pre-push_Hook_Run_Before_Pus](https://img.shields.io/badge/pre-push_Hook_Run_Before_Pus-F05032?style=flat-square&logo=git&logoColor=white)](#pre-push-hook-run-before-pushing)
[![post-merge_Hook_Auto-install](https://img.shields.io/badge/post-merge_Hook_Auto-install-F05032?style=flat-square&logo=git&logoColor=white)](#post-merge-hook-auto-install-dependencies)
[![Sharing_Hooks_with_the_Team_](https://img.shields.io/badge/Sharing_Hooks_with_the_Team_-F05032?style=flat-square&logo=git&logoColor=white)](#sharing-hooks-with-the-team-husky-nodejs)
[![Lab_Task](https://img.shields.io/badge/Lab_Task-F05032?style=flat-square&logo=git&logoColor=white)](#lab-task)

</div>

---
> **Goal:** Automate quality gates with client-side and server-side Git hooks.

---

## 🪝 What are Git Hooks?

Git hooks are **shell scripts that run automatically** when Git events happen. They enforce rules before code ever touches the remote.

```
git commit → pre-commit hook → commit-msg hook → commit saved
git push   → pre-push hook   → code sent to GitHub
```

---

## 📂 Hook Locations

```bash
# Hooks live in:
ls .git/hooks/

# You'll see sample files like:
# pre-commit.sample
# commit-msg.sample
# pre-push.sample

# To activate a hook: remove .sample extension
cp .git/hooks/pre-commit.sample .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

---

## 🛡️ pre-commit Hook (Run Before Every Commit)

Prevent bad code from being committed:

```bash
#!/bin/bash
# .git/hooks/pre-commit

echo "🔍 Running pre-commit checks..."

# Check for debug statements
if grep -r "console.log\|debugger\|pdb.set_trace()" --include="*.js" --include="*.py" .; then
    echo "❌ Remove debug statements before committing!"
    exit 1
fi

# Check for TODO/FIXME in new code
if git diff --cached | grep -E "^\+.*TODO|^\+.*FIXME"; then
    echo "⚠️  New TODOs detected. Commit anyway? Press Ctrl+C to abort, or Enter to continue"
    read
fi

# Run tests
if [ -f "package.json" ]; then
    npm test --silent
    if [ $? -ne 0 ]; then
        echo "❌ Tests failed. Fix them before committing."
        exit 1
    fi
fi

echo "✅ Pre-commit checks passed!"
exit 0
```

---

## ✍️ commit-msg Hook (Validate Commit Format)

Enforce conventional commit format:

```bash
#!/bin/bash
# .git/hooks/commit-msg

COMMIT_MSG_FILE=$1
COMMIT_MSG=$(cat "$COMMIT_MSG_FILE")

# Conventional commit pattern
PATTERN="^(feat|fix|docs|style|refactor|test|chore|perf|ci|build|revert)(\(.+\))?: .{1,100}$"

if ! echo "$COMMIT_MSG" | grep -qE "$PATTERN"; then
    echo "❌ Invalid commit message format!"
    echo ""
    echo "Required format: <type>(<scope>): <description>"
    echo "Types: feat|fix|docs|style|refactor|test|chore|perf|ci|build|revert"
    echo ""
    echo "Examples:"
    echo "  feat(auth): add JWT token validation"
    echo "  fix: correct null pointer in user service"
    echo "  docs: update README installation steps"
    echo ""
    echo "Your message: $COMMIT_MSG"
    exit 1
fi

echo "✅ Commit message format valid!"
exit 0
```

---

## 🚀 pre-push Hook (Run Before Pushing)

```bash
#!/bin/bash
# .git/hooks/pre-push

echo "🚀 Running pre-push checks..."

BRANCH=$(git rev-parse --abbrev-ref HEAD)

# Prevent pushing directly to main or master
if [ "$BRANCH" = "main" ] || [ "$BRANCH" = "master" ]; then
    echo "❌ Direct push to $BRANCH is not allowed!"
    echo "   Create a feature branch and open a Pull Request."
    exit 1
fi

# Run full test suite
if [ -f "pytest.ini" ] || [ -f "setup.py" ]; then
    python -m pytest tests/ -q
    if [ $? -ne 0 ]; then
        echo "❌ Tests failed. Fix before pushing."
        exit 1
    fi
fi

echo "✅ Pre-push checks passed!"
exit 0
```

---

## 🔁 post-merge Hook (Auto-install Dependencies)

```bash
#!/bin/bash
# .git/hooks/post-merge

echo "🔄 Running post-merge setup..."

# Check if package.json changed
CHANGED_FILES=$(git diff-tree -r --name-only --no-commit-id ORIG_HEAD HEAD)

if echo "$CHANGED_FILES" | grep -q "^package.json$"; then
    echo "📦 package.json changed — running npm install..."
    npm install
fi

if echo "$CHANGED_FILES" | grep -q "^requirements.txt$"; then
    echo "🐍 requirements.txt changed — running pip install..."
    pip install -r requirements.txt
fi

echo "✅ Post-merge setup complete!"
```

---

## 🌍 Sharing Hooks with the Team: Husky (Node.js)

Since `.git/hooks/` is not committed, use **Husky** to share hooks:

```bash
# Install Husky
npm install --save-dev husky
npx husky init

# Add pre-commit hook
echo "npm run lint" > .husky/pre-commit
echo "npm test" >> .husky/pre-commit
chmod +x .husky/pre-commit

# Add commit-msg hook
cat > .husky/commit-msg << 'EOF'
#!/bin/sh
npx commitlint --edit $1
EOF
chmod +x .husky/commit-msg

# package.json - add prepare script
# "prepare": "husky"
```

### Python Alternative: pre-commit Framework

```bash
# Install pre-commit
pip install pre-commit

# Create .pre-commit-config.yaml
cat > .pre-commit-config.yaml << 'EOF'
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-json
      - id: detect-private-key
      - id: check-merge-conflict

  - repo: https://github.com/psf/black
    rev: 23.12.1
    hooks:
      - id: black

  - repo: https://github.com/pycqa/flake8
    rev: 7.0.0
    hooks:
      - id: flake8
EOF

# Install the hooks
pre-commit install

# Run manually on all files
pre-commit run --all-files
```

---

## 🧪 Lab Task

```bash
# Step 1: Create a commit-msg hook
cat > .git/hooks/commit-msg << 'EOF'
#!/bin/bash
COMMIT_MSG=$(cat "$1")
PATTERN="^(feat|fix|docs|chore|test|style|refactor)(\(.+\))?: .{1,100}$"
if ! echo "$COMMIT_MSG" | grep -qE "$PATTERN"; then
    echo "❌ Bad commit message: $COMMIT_MSG"
    echo "Use: feat|fix|docs|chore(<scope>): description"
    exit 1
fi
exit 0
EOF
chmod +x .git/hooks/commit-msg

# Step 2: Test with a bad commit message
echo "test" > test-hook.txt
git add test-hook.txt
git commit -m "bad message"    # Should FAIL

# Step 3: Test with a good commit message
git commit -m "chore: test commit-msg hook"   # Should PASS

# Step 4: Create a pre-commit hook that checks for secrets
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash
if git diff --cached | grep -iE "password\s*=\s*['\"][^'\"]+['\"]|api_key\s*=|secret\s*="; then
    echo "❌ Possible secret detected in staged files!"
    exit 1
fi
exit 0
EOF
chmod +x .git/hooks/pre-commit

# Test the secret detection
echo "password = 'hunter2'" > secret-test.txt
git add secret-test.txt
git commit -m "test: check secret detection"  # Should FAIL
rm secret-test.txt
git checkout -- .
```

✅ **Check:** Bad commit messages and secret files are blocked by your hooks.
---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_GitHub_CLI_Pro-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-13-github-cli-pro.md)
[![Pro_Index](https://img.shields.io/badge/Pro_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Advanced_Actions_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-15-advanced-actions.md)

</div>