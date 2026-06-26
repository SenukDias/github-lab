# 🔴 Lab 16 — Security & Access Control

> **Goal:** Lock down your repositories with branch protection, secret scanning, and security policies.

---

## 🔒 Branch Protection Rules

Prevent accidental pushes to important branches:

```bash
# Enable branch protection via gh CLI
gh api repos/{owner}/{repo}/branches/main/protection \
  --method PUT \
  --input - << 'EOF'
{
  "required_status_checks": {
    "strict": true,
    "contexts": ["ci/test", "ci/lint"]
  },
  "enforce_admins": true,
  "required_pull_request_reviews": {
    "required_approving_review_count": 2,
    "dismiss_stale_reviews": true,
    "require_code_owner_reviews": true,
    "require_last_push_approval": true
  },
  "restrictions": null,
  "required_linear_history": true,
  "allow_force_pushes": false,
  "allow_deletions": false,
  "block_creations": false,
  "required_conversation_resolution": true,
  "lock_branch": false
}
EOF

# View current protection
gh api repos/{owner}/{repo}/branches/main/protection
```

---

## 👥 CODEOWNERS File

Auto-assign reviewers based on which files are changed:

```bash
# Create .github/CODEOWNERS
cat > .github/CODEOWNERS << 'EOF'
# Default owner for everything
* @team-lead

# Backend code requires backend review
/src/api/ @backend-team
/src/database/ @backend-team @dba-team

# Frontend requires frontend review
/src/ui/ @frontend-team
/src/components/ @frontend-team

# Security-sensitive files require security review
/src/auth/ @security-team
/.github/workflows/ @devops-team @security-team

# Documentation
/docs/ @tech-writers
*.md @tech-writers
EOF

git add .github/CODEOWNERS
git commit -m "chore: add CODEOWNERS for automatic review assignment"
```

---

## 🔑 Secrets Management

```bash
# Add repository secrets
gh secret set DATABASE_URL
gh secret set JWT_SECRET_KEY --body "$(openssl rand -hex 32)"
gh secret set STRIPE_API_KEY

# Add organization-level secret (visible to all repos)
gh secret set ORG_DEPLOY_KEY --org myorg

# Add environment-specific secrets
gh secret set DATABASE_URL --env staging
gh secret set DATABASE_URL --env production

# List secrets (names only — values are masked)
gh secret list
gh secret list --env production

# Remove a secret
gh secret delete OLD_API_KEY
```

### Using Secrets in Workflows

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production    # Requires approval before secrets are exposed
    steps:
      - name: Deploy
        env:
          DB_URL: ${{ secrets.DATABASE_URL }}
          JWT_KEY: ${{ secrets.JWT_SECRET_KEY }}
        run: ./deploy.sh
```

---

## 🔍 Secret Scanning & Push Protection

GitHub automatically scans for leaked secrets:

```yaml
# Enable in GitHub Settings → Security → Code security and analysis
# Or configure via API:
gh api repos/{owner}/{repo} \
  --method PATCH \
  --field secret_scanning='{"status":"enabled"}' \
  --field secret_scanning_push_protection='{"status":"enabled"}'
```

### What GitHub Scans For:

- AWS Access Keys (`AKIA...`)
- GitHub Personal Access Tokens (`ghp_...`)
- Stripe API Keys (`sk_live_...`)
- Google API Keys
- Azure Service Principal credentials
- And 200+ more patterns

---

## 🧰 Dependabot — Automated Security Updates

```yaml
# .github/dependabot.yml
version: 2
updates:
  # Node.js dependencies
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
      day: "monday"
      time: "09:00"
    reviewers:
      - "devops-team"
    labels:
      - "dependencies"
      - "security"
    open-pull-requests-limit: 10

  # Python dependencies
  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"
    ignore:
      - dependency-name: "boto3"    # Manually managed
        versions: ["*"]

  # GitHub Actions versions
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```

---

## 🛡️ Security Policy

```bash
# Create SECURITY.md
cat > SECURITY.md << 'EOF'
# Security Policy

## Supported Versions

| Version | Supported |
|---------|-----------|
| 2.x     | ✅ |
| 1.x     | ⚠️ Critical only |
| < 1.0   | ❌ |

## Reporting a Vulnerability

**Please do NOT open a public issue for security vulnerabilities.**

Instead, email: security@yourcompany.com

Include:
- Description of the vulnerability
- Steps to reproduce
- Potential impact
- Suggested fix (if any)

We will respond within 48 hours and coordinate a responsible disclosure.
EOF

git add SECURITY.md
git commit -m "docs: add security policy"
```

---

## 🔐 Environment Protection Rules

```bash
# Create environments with protection
gh api repos/{owner}/{repo}/environments/production \
  --method PUT \
  --input - << 'EOF'
{
  "wait_timer": 0,
  "reviewers": [
    {
      "type": "User",
      "id": YOUR_USER_ID
    }
  ],
  "deployment_branch_policy": {
    "protected_branches": true,
    "custom_branch_policies": false
  }
}
EOF
```

---

## 📊 Audit Logs

```bash
# View org audit log via API
gh api /orgs/{org}/audit-log \
  --paginate \
  --jq '.[] | select(.action | startswith("repo.")) | "\(.@timestamp) \(.action) \(.actor)"'

# Filter specific events
gh api /orgs/{org}/audit-log \
  --field phrase="action:repo.create" \
  --jq '.[].actor'
```

---

## 🧪 Lab Task

```bash
# Task 1: Create CODEOWNERS
mkdir -p .github
cat > .github/CODEOWNERS << 'EOF'
# All files need review from owner
* @$(gh api user --jq '.login')

# Workflows need special review
/.github/workflows/ @$(gh api user --jq '.login')
EOF

git add .github/CODEOWNERS
git commit -m "chore: add CODEOWNERS"
git push

# Task 2: Add repository variables (non-secret config)
gh variable set APP_NAME --body "github-docker-lab"
gh variable set ENVIRONMENT --body "lab"
gh variable list

# Task 3: Create security scanning workflow
cat > .github/workflows/security.yml << 'EOF'
name: Security Scan

on:
  push:
    branches: [main]
  schedule:
    - cron: '0 6 * * 1'   # Every Monday 6 AM

jobs:
  codeql:
    name: CodeQL Analysis
    runs-on: ubuntu-latest
    permissions:
      security-events: write
    steps:
      - uses: actions/checkout@v4
      - uses: github/codeql-action/init@v3
        with:
          languages: javascript, python
      - uses: github/codeql-action/autobuild@v3
      - uses: github/codeql-action/analyze@v3
EOF

git add .github/
git commit -m "ci: add CodeQL security scanning"
git push

# Task 4: View security alerts
gh api repos/{owner}/{repo}/vulnerability-alerts --method GET
```

✅ **Check:** Your repo has CODEOWNERS, variables, and a security scanning workflow.

---

## 🏁 Pro Track Complete!

You've mastered:
- ✅ Cherry-pick, bisect, reflog, worktrees
- ✅ Advanced gh CLI scripting and API access
- ✅ Git hooks for automated quality gates
- ✅ Full CI/CD multi-stage pipelines
- ✅ Branch protection, secrets, CODEOWNERS, security scanning

---

➡️ **[View All Tasks →](../tasks/)**
