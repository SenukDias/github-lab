# 📋 Pro Tasks

These tasks reflect real-world engineering challenges. No hand-holding here.

---

## Task 1 — Advanced Git Archaeology

You have a repository with 20+ commits. Your challenge:

```bash
# Setup: Create a repo with a bug introduced at some point
mkdir git-archaeology && cd git-archaeology
git init

for i in {1..10}; do
  echo "Feature $i works fine" >> app.txt
  git add app.txt
  git commit -m "feat: add feature $i"
done

# Introduce a "bug" at commit 11
echo "BUG_INTRODUCED=true" >> config.txt
git add config.txt
git commit -m "chore: update config"

for i in {12..20}; do
  echo "Feature $i works fine" >> app.txt
  git add app.txt
  git commit -m "feat: add feature $i"
done
```

**Your tasks:**
1. Use `git bisect` to find exactly which commit introduced `BUG_INTRODUCED=true`
2. Cherry-pick the fix from a different branch to main WITHOUT merging the whole branch
3. Use `git log --S "BUG_INTRODUCED"` (pickaxe search) to find commits touching that string
4. Use reflog to restore a branch you intentionally deleted

---

## Task 2 — The Hook Suite

Build a complete client-side hook system:

**Requirements:**

1. **`pre-commit` hook** that:
   - Blocks any file >1MB
   - Detects passwords/API keys using regex
   - Runs `flake8` or `eslint` if those tools exist
   - Exits 0 only if all checks pass

2. **`commit-msg` hook** that:
   - Enforces conventional commit format
   - Blocks commits with messages under 10 characters
   - Adds the current branch name as scope if no scope given
     - e.g., on branch `feature/login`, `feat: add button` becomes `feat(login): add button`

3. **`pre-push` hook** that:
   - Blocks pushing to `main` directly
   - Ensures all tests pass
   - Checks that commit count on feature branch is ≤ 10 (suggest squashing)

All hooks must output colored, human-friendly messages.

---

## Task 3 — Full CI/CD Pipeline

Build a complete CI/CD pipeline for a Python Flask app:

```python
# app.py
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/health')
def health():
    return jsonify({"status": "ok", "version": "1.0.0"})

@app.route('/users')
def users():
    return jsonify({"users": ["alice", "bob", "charlie"]})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

```python
# tests/test_app.py
import pytest
from app import app

@pytest.fixture
def client():
    app.config['TESTING'] = True
    with app.test_client() as client:
        yield client

def test_health(client):
    rv = client.get('/health')
    assert rv.status_code == 200
    assert b'ok' in rv.data

def test_users(client):
    rv = client.get('/users')
    assert rv.status_code == 200
```

**Your CI/CD pipeline must:**

- [ ] Run on push to `main` and `develop`, and on all PRs
- [ ] Job 1: Lint (flake8 + black --check)
- [ ] Job 2: Test with matrix (Python 3.10, 3.11, 3.12) — needs Job 1
- [ ] Job 3: Build Docker image — needs Job 2, only on `main`
- [ ] Job 4: Deploy to "staging" environment (can be a fake deploy that prints "deployed") — needs Job 3
- [ ] Use pip caching
- [ ] Upload test results as artifacts
- [ ] Add workflow summary with test counts

**Bonus:**
- Add a nightly scheduled run of the full pipeline
- Add CodeQL security scanning
- Add Dependabot for pip packages

---

## Task 4 — GitHub CLI Automation Script

Write a shell script `repo-health-check.sh` that:

```bash
#!/bin/bash
# Checks the health of a GitHub repository and produces a report

REPO=${1:-$(gh repo view --json nameWithOwner --jq '.nameWithOwner')}

echo "📊 Repository Health Report: $REPO"
echo "Generated: $(date)"
echo "================================"

# Check 1: Has branch protection on main?
# Check 2: Has CODEOWNERS?
# Check 3: Has CI workflow?
# Check 4: Last commit age (warn if > 30 days)
# Check 5: Open PRs older than 7 days
# Check 6: Open issues older than 30 days
# Check 7: Dependabot enabled?
# Check 8: Secret scanning enabled?

# Output color-coded ✅/⚠️/❌ for each check
# Exit code 0 if all pass, 1 if any fail
```

The script should be invokable as:
```bash
./repo-health-check.sh              # Check current repo
./repo-health-check.sh org/repo    # Check any repo
```

---

## Task 5 — Monorepo Management

Create a monorepo with three services and a shared CI/CD strategy:

```
monorepo/
├── services/
│   ├── auth-service/
│   │   ├── src/main.py
│   │   └── tests/
│   ├── user-service/
│   │   ├── src/main.py
│   │   └── tests/
│   └── notification-service/
│       ├── src/main.py
│       └── tests/
├── shared/
│   └── utils.py
└── .github/
    └── workflows/
        └── monorepo-ci.yml
```

**The workflow must:**
1. Detect which service(s) changed using `paths` filter
2. Only run tests for the changed service(s)
3. If `shared/` changes, run ALL service tests
4. Build Docker images only for changed services
5. Tag images with `service-name:git-sha`

---

## 🏆 Final Submission

```bash
echo "=== PRO TRACK FINAL SUBMISSION ==="
echo "Candidate: $(gh api user --jq '.login')"
echo ""
echo "--- Repository Stats ---"
gh api user/repos --jq '
  . | length as $total |
  map(select(.fork == false)) | length as $original |
  "Total repos: \($total), Original: \($original)"'

echo ""
echo "--- CI/CD Runs (last 7 days) ---"
gh run list --limit 20 --json status,conclusion,workflowName,createdAt \
  --jq '.[] | select(.createdAt > (now - 604800 | todate)) | "\(.conclusion // .status) \(.workflowName)"' \
  | sort | uniq -c | sort -rn

echo ""
echo "--- Recent Releases ---"
gh release list --limit 5

echo ""
echo "--- Pro Skills Demonstrated ---"
echo "✅ Advanced Git (cherry-pick, bisect, reflog)"
echo "✅ Git Hooks (pre-commit, commit-msg, pre-push)"
echo "✅ Full CI/CD Pipeline"
echo "✅ gh CLI Automation"
echo "✅ Security Configuration"
```
