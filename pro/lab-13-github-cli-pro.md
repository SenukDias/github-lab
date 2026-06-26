# 🔴 Lab 13 — GitHub CLI Power Usage


---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Advanced_Git-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-12-advanced-git.md)
[![Pro_Index](https://img.shields.io/badge/Pro_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Git_Hooks_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-14-git-hooks.md)

</div>

---

<div align="center">

**Jump to section:**

[![gh_CLI_Architecture](https://img.shields.io/badge/gh_CLI_Architecture-F05032?style=flat-square&logo=git&logoColor=white)](#gh-cli-architecture)
[![GitHub_API_with_gh_api](https://img.shields.io/badge/GitHub_API_with_gh_api-F05032?style=flat-square&logo=git&logoColor=white)](#github-api-with-gh-api)
[![Releases_%26_Tags](https://img.shields.io/badge/Releases_%26_Tags-F05032?style=flat-square&logo=git&logoColor=white)](#releases-tags)
[![Advanced_Search_%26_Filterin](https://img.shields.io/badge/Advanced_Search_%26_Filterin-F05032?style=flat-square&logo=git&logoColor=white)](#advanced-search-filtering)
[![Scripting_with_gh_CLI](https://img.shields.io/badge/Scripting_with_gh_CLI-F05032?style=flat-square&logo=git&logoColor=white)](#scripting-with-gh-cli)
[![gh_CLI_Extensions](https://img.shields.io/badge/gh_CLI_Extensions-F05032?style=flat-square&logo=git&logoColor=white)](#gh-cli-extensions)
[![Lab_Task](https://img.shields.io/badge/Lab_Task-F05032?style=flat-square&logo=git&logoColor=white)](#lab-task)

</div>

---
> **Goal:** Use `gh` CLI for scripting, automation, and complex GitHub workflows.

---

## 🔧 gh CLI Architecture

```bash
gh [command] [subcommand] [flags]

# Main command groups:
gh repo           # Repository operations
gh pr             # Pull requests
gh issue          # Issues
gh workflow       # GitHub Actions
gh run            # Workflow runs
gh release        # Releases and tags
gh api            # Raw GitHub REST/GraphQL API
gh auth           # Authentication
gh ssh-key        # SSH keys
gh secret         # Repository secrets
gh variable       # Actions variables
gh codespace      # GitHub Codespaces
gh extension      # gh CLI extensions
```

---

## 🔌 GitHub API with `gh api`

The `gh api` command gives you **full access to the GitHub REST API**:

```bash
# Get your user info
gh api user

# Get a repo's details
gh api repos/microsoft/vscode

# Filter with jq
gh api user --jq '.login, .name, .public_repos, .followers'

# List all repos with star count
gh api user/repos --jq '.[] | "\(.name): ⭐ \(.stargazers_count)"'

# Get a repo's latest release
gh api repos/cli/cli/releases/latest --jq '.tag_name, .published_at'

# Pagination
gh api repos/microsoft/vscode/issues --paginate --jq '.[].title'

# POST/PATCH/DELETE
gh api repos/OWNER/REPO/issues \
  --method POST \
  --field title="Bug report" \
  --field body="Steps to reproduce..." \
  --field labels='["bug"]'

# GraphQL query
gh api graphql -f query='
{
  viewer {
    login
    repositories(first: 5, orderBy: {field: UPDATED_AT, direction: DESC}) {
      nodes {
        name
        stargazerCount
        primaryLanguage {
          name
        }
      }
    }
  }
}'
```

---

## 📦 Releases & Tags

```bash
# Create a release
gh release create v1.0.0 \
  --title "Version 1.0.0" \
  --notes "## What's new
  - Feature A
  - Bug fix B" \
  --target main

# Create with attached files
gh release create v1.0.0 \
  dist/app-linux-x64 \
  dist/app-macos-arm64 \
  dist/app-win.exe \
  --title "v1.0.0" \
  --generate-notes     # Auto-generate from PRs/commits

# Create a pre-release
gh release create v2.0.0-beta.1 --prerelease

# List releases
gh release list

# Download a release asset
gh release download v1.0.0 --pattern "*.exe"
gh release download v1.0.0 --dir ./downloads

# Delete a release
gh release delete v0.9.0 --yes

# View release
gh release view v1.0.0
```

---

## 🔍 Advanced Search & Filtering

```bash
# Search repos
gh search repos "machine learning" --language python --stars ">1000" --limit 10

# Search issues across all repos
gh search issues "memory leak" --repo microsoft/vscode --state open

# Search PRs
gh search prs "authentication" --author octocat --state merged

# Search commits
gh search commits "fix sql injection" --repo OWNER/REPO

# List PRs with filters
gh pr list --state all --author @me --limit 20
gh pr list --label "needs-review" --assignee @me
gh pr list --base main --draft

# Filter runs
gh run list --workflow ci.yml --status failure
gh run list --branch main --limit 5
```

---

## 📜 Scripting with gh CLI

```bash
# Script: Auto-close stale issues
#!/bin/bash
STALE_ISSUES=$(gh issue list --state open --json number,updatedAt \
  --jq '.[] | select(.updatedAt < (now - 2592000 | todate)) | .number')

for issue in $STALE_ISSUES; do
  gh issue close $issue --comment "Closing due to inactivity. Reopen if still relevant."
  echo "Closed issue #$issue"
done
```

```bash
# Script: Create a release from git tags
#!/bin/bash
VERSION=$(git describe --tags --abbrev=0)
PREV_VERSION=$(git describe --tags --abbrev=0 HEAD~1)

gh release create $VERSION \
  --generate-notes \
  --notes-start-tag $PREV_VERSION \
  --title "Release $VERSION"
```

```bash
# Script: Sync all your forks
#!/bin/bash
gh repo list --fork --json nameWithOwner,parent --jq '.[] | .nameWithOwner + " " + .parent.nameWithOwner' | \
while read fork parent; do
  echo "Syncing $fork from $parent..."
  gh repo sync $fork --source $parent
done
```

---

## 🔌 gh CLI Extensions

```bash
# List available extensions
gh extension search

# Install an extension
gh extension install dlvhdr/gh-dash        # Dashboard
gh extension install nicholasgasior/gscale # PR dashboard
gh extension install mislav/gh-branch      # Branch utilities

# List installed extensions
gh extension list

# Use an extension
gh dash           # Opens a TUI dashboard

# Remove extension
gh extension remove dlvhdr/gh-dash

# Create your own extension
gh extension create my-extension
```

---

## 🧪 Lab Task

```bash
# Task 1: Use gh api to get advanced repo stats
gh api repos/$GITHUB_USERNAME/github-docker-lab \
  --jq '{ name: .name, stars: .stargazers_count, forks: .forks_count, issues: .open_issues_count }'

# Task 2: Create a release
git tag -a v0.1.0 -m "First lab release"
git push origin v0.1.0

gh release create v0.1.0 \
  --title "Lab Session v0.1.0" \
  --notes "## Lab Progress
  - Completed beginner labs ✅
  - Completed intermediate labs ✅
  - Starting pro labs 🔴"

# Task 3: List workflow runs with status
gh run list --limit 5

# Task 4: Write a script that lists all your open PRs
gh api --paginate graphql -f query='
{
  viewer {
    pullRequests(states: OPEN, first: 10) {
      nodes {
        title
        repository { nameWithOwner }
        createdAt
      }
    }
  }
}' --jq '.data.viewer.pullRequests.nodes[] | "\(.repository.nameWithOwner) — \(.title)"'
```

✅ **Check:** You have a release `v0.1.0` visible in `gh release list`.
---

<div align="center">

[![← Prev](https://img.shields.io/badge/←_Advanced_Git-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-12-advanced-git.md)
[![Pro_Index](https://img.shields.io/badge/Pro_Index-e05030?style=for-the-badge&logo=git&logoColor=white)](README.md)
[![Next →](https://img.shields.io/badge/Git_Hooks_→-F05032?style=for-the-badge&logo=git&logoColor=white)](lab-14-git-hooks.md)

</div>