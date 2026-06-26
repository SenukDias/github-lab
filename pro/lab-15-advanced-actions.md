# 🔴 Lab 15 — Advanced GitHub Actions: Full CI/CD Pipeline


---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Git_Hooks-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-14-git-hooks.md)
[![Pro_Index](https://img.shields.io/badge/Pro_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Security_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-16-security.md)

</div>

---

<div align="center">

**Jump to section:**

[![Pipeline_Architecture](https://img.shields.io/badge/Pipeline_Architecture-F05032?style=flat-square&logo=git&logoColor=white)](#pipeline-architecture)
[![Complete_Python_CICD_Workflo](https://img.shields.io/badge/Complete_Python_CICD_Workflo-F05032?style=flat-square&logo=git&logoColor=white)](#complete-python-cicd-workflow)
[![Reusable_Workflows](https://img.shields.io/badge/Reusable_Workflows-F05032?style=flat-square&logo=git&logoColor=white)](#reusable-workflows)
[![Caching_Dependencies](https://img.shields.io/badge/Caching_Dependencies-F05032?style=flat-square&logo=git&logoColor=white)](#caching-dependencies)
[![Lab_Task](https://img.shields.io/badge/Lab_Task-F05032?style=flat-square&logo=git&logoColor=white)](#lab-task)

</div>

---
> **Goal:** Build a production-grade CI/CD pipeline with caching, artifacts, environments, and deployment.

---

## 🏗️ Pipeline Architecture

```
Push to feature branch
        ↓
 [CI: lint + test]    ← fast feedback
        ↓ (pass)
[Build Docker image]  ← package the app
        ↓
   Push to PR
        ↓
 [Review + Approve]
        ↓
  Merge to main
        ↓
[Deploy to Staging]   ← auto deploy
        ↓ (manual gate)
[Deploy to Production]
```

---

## 🐍 Complete Python CI/CD Workflow

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop, 'feature/**']
  pull_request:
    branches: [main]
  release:
    types: [published]

env:
  PYTHON_VERSION: '3.11'
  IMAGE_NAME: my-app

jobs:
  # ─── Job 1: Lint ─────────────────────────────────────────
  lint:
    name: 🔍 Lint & Format
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: ${{ env.PYTHON_VERSION }}
          cache: 'pip'

      - run: pip install flake8 black isort mypy

      - name: Check formatting (black)
        run: black --check .

      - name: Check import order (isort)
        run: isort --check-only .

      - name: Lint (flake8)
        run: flake8 . --max-line-length=120

  # ─── Job 2: Test ─────────────────────────────────────────
  test:
    name: 🧪 Test (Python ${{ matrix.python-version }})
    runs-on: ${{ matrix.os }}
    needs: lint
    strategy:
      fail-fast: false
      matrix:
        python-version: ['3.10', '3.11', '3.12']
        os: [ubuntu-latest, windows-latest]

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
          cache: 'pip'

      - name: Install dependencies
        run: |
          pip install --upgrade pip
          pip install -r requirements.txt
          pip install pytest pytest-cov

      - name: Run tests with coverage
        run: pytest tests/ --cov=src --cov-report=xml --cov-report=html -v

      - name: Upload coverage report
        uses: actions/upload-artifact@v4
        with:
          name: coverage-${{ matrix.os }}-py${{ matrix.python-version }}
          path: htmlcov/
          retention-days: 7

      - name: Check coverage threshold
        run: |
          COVERAGE=$(python -m pytest --co -q | grep -oP '\d+%' | tail -1 | tr -d '%')
          if [ "$COVERAGE" -lt "80" ]; then
            echo "Coverage $COVERAGE% is below 80% threshold"
            exit 1
          fi

  # ─── Job 3: Security Scan ────────────────────────────────
  security:
    name: 🔒 Security Scan
    runs-on: ubuntu-latest
    needs: lint
    steps:
      - uses: actions/checkout@v4

      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          format: 'sarif'
          output: 'trivy-results.sarif'

      - name: Upload Trivy results to GitHub Security
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: 'trivy-results.sarif'

  # ─── Job 4: Build Docker Image ───────────────────────────
  build:
    name: 🐳 Build & Push Docker Image
    runs-on: ubuntu-latest
    needs: [test, security]
    if: github.ref == 'refs/heads/main' || github.event_name == 'release'
    outputs:
      image-tag: ${{ steps.meta.outputs.tags }}
      image-digest: ${{ steps.build.outputs.digest }}

    steps:
      - uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Login to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Extract Docker metadata (tags, labels)
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ghcr.io/${{ github.repository }}/${{ env.IMAGE_NAME }}
          tags: |
            type=sha
            type=ref,event=branch
            type=semver,pattern={{version}}
            type=raw,value=latest,enable={{is_default_branch}}

      - name: Build and push
        id: build
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
          platforms: linux/amd64,linux/arm64

  # ─── Job 5: Deploy to Staging ────────────────────────────
  deploy-staging:
    name: 🚀 Deploy to Staging
    runs-on: ubuntu-latest
    needs: build
    environment:
      name: staging
      url: https://staging.myapp.com

    steps:
      - uses: actions/checkout@v4

      - name: Deploy to staging
        run: |
          echo "Deploying ${{ needs.build.outputs.image-tag }} to staging..."
          # kubectl set image deployment/myapp myapp=${{ needs.build.outputs.image-tag }}
          # or: helm upgrade myapp ./charts/myapp --set image.tag=${{ needs.build.outputs.image-tag }}

      - name: Run smoke tests
        run: |
          sleep 10
          curl -f https://staging.myapp.com/health || exit 1

  # ─── Job 6: Deploy to Production (Manual Gate) ──────────
  deploy-production:
    name: 🏭 Deploy to Production
    runs-on: ubuntu-latest
    needs: deploy-staging
    environment:
      name: production
      url: https://myapp.com
    if: github.event_name == 'release'

    steps:
      - name: Deploy to production
        run: |
          echo "Deploying ${{ needs.build.outputs.image-tag }} to production..."
```

---

## 🔄 Reusable Workflows

```yaml
# .github/workflows/reusable-test.yml
name: Reusable Test Workflow

on:
  workflow_call:
    inputs:
      python-version:
        required: true
        type: string
    secrets:
      TEST_API_KEY:
        required: true

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: ${{ inputs.python-version }}
      - run: pytest tests/
        env:
          API_KEY: ${{ secrets.TEST_API_KEY }}
```

```yaml
# .github/workflows/ci.yml — calling the reusable workflow
name: CI
on: [push]
jobs:
  call-test:
    uses: ./.github/workflows/reusable-test.yml
    with:
      python-version: '3.11'
    secrets:
      TEST_API_KEY: ${{ secrets.TEST_API_KEY }}
```

---

## 📦 Caching Dependencies

```yaml
# Cache pip packages
- uses: actions/cache@v4
  with:
    path: ~/.cache/pip
    key: ${{ runner.os }}-pip-${{ hashFiles('requirements.txt') }}
    restore-keys: ${{ runner.os }}-pip-

# Cache node_modules
- uses: actions/cache@v4
  with:
    path: node_modules
    key: ${{ runner.os }}-node-${{ hashFiles('package-lock.json') }}

# Cache Maven packages
- uses: actions/cache@v4
  with:
    path: ~/.m2
    key: ${{ runner.os }}-maven-${{ hashFiles('pom.xml') }}
```

---

## 🧪 Lab Task

```bash
# Create a multi-job CI workflow for your lab repo
cat > .github/workflows/lab-ci-advanced.yml << 'EOF'
name: Advanced Lab CI

on:
  push:
    branches: [main, 'feature/**']
  pull_request:
    branches: [main]

jobs:
  validate:
    name: Validate Lab Files
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Check required files exist
        run: |
          FILES=(
            "README.md"
            "beginner/lab-01-installation.md"
            "intermediate/lab-07-branching.md"
            "pro/lab-12-advanced-git.md"
          )
          for f in "${FILES[@]}"; do
            [ -f "$f" ] && echo "✅ $f" || { echo "❌ Missing: $f"; exit 1; }
          done

      - name: Count labs
        run: |
          TOTAL=$(find . -name "lab-*.md" | wc -l)
          echo "📚 Total labs: $TOTAL"
          echo "TOTAL_LABS=$TOTAL" >> $GITHUB_STEP_SUMMARY

      - name: Generate summary
        run: |
          echo "## Lab Session Summary 📊" >> $GITHUB_STEP_SUMMARY
          echo "| Level | Labs |" >> $GITHUB_STEP_SUMMARY
          echo "|-------|------|" >> $GITHUB_STEP_SUMMARY
          echo "| 🟢 Beginner | $(ls beginner/lab-*.md | wc -l) |" >> $GITHUB_STEP_SUMMARY
          echo "| 🟡 Intermediate | $(ls intermediate/lab-*.md | wc -l) |" >> $GITHUB_STEP_SUMMARY
          echo "| 🔴 Pro | $(ls pro/lab-*.md | wc -l) |" >> $GITHUB_STEP_SUMMARY
EOF

git add .github/
git commit -m "ci: add advanced multi-job workflow"
git push

# Watch it run
gh run list --workflow lab-ci-advanced.yml
gh run watch
```

✅ **Check:** Workflow summary shows all lab counts in the GitHub Actions UI.
---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Git_Hooks-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-14-git-hooks.md)
[![Pro_Index](https://img.shields.io/badge/Pro_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Security_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-16-security.md)

</div>