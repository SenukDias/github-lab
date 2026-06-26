# 📋 Beginner Tasks

Complete these tasks to prove you've mastered the beginner level.

---

## ✅ Task Checklist

### Task 1 — Your First Repository

Create a repository called `my-github-lab` with the following files:

```
my-github-lab/
├── README.md        (describe what you learned)
├── hello.py         (prints "Hello, GitHub!")
├── notes.txt        (at least 5 things you learned today)
└── .gitignore       (ignore *.pyc and __pycache__)
```

Commands to run:
```bash
gh repo create my-github-lab --public --clone
cd my-github-lab
# Create the files above
git add .
git commit -m "feat: initial lab submission"
git push
```

---

### Task 2 — The Git Workflow

In your repo, practice the full workflow:

1. Create a new file `update.txt` with the text `"Version 1"`
2. Stage and commit with message `"feat: add update file"`
3. Edit `update.txt` to say `"Version 2"`
4. Use `git diff` to see the change
5. Stage only `update.txt` (not everything)
6. Commit with message `"feat: update to version 2"`
7. View your log with `git log --oneline`

```bash
# Expected log output:
# abc1234 feat: update to version 2
# def5678 feat: add update file
# ghi9012 feat: initial lab submission
```

---

### Task 3 — Recovery with Reflog

1. Make a commit with `echo "oops" > oops.txt && git add . && git commit -m "chore: oops commit"`
2. Hard reset to remove it: `git reset --hard HEAD~1`
3. Verify the file is gone: `ls oops.txt`
4. Use `git reflog` to find the deleted commit
5. Recover it using `git reset --hard HEAD@{1}`
6. Verify the file is back: `ls oops.txt`

---

### Task 4 — The .gitignore Challenge

Create a project with these files, but only commit the ones that are appropriate:

```
project/
├── app.py              ✅ Commit
├── requirements.txt    ✅ Commit
├── README.md           ✅ Commit
├── .env                ❌ Don't commit (secrets)
├── __pycache__/        ❌ Don't commit (build artifacts)
├── venv/               ❌ Don't commit (dependencies)
└── debug.log           ❌ Don't commit (log files)
```

Use `git status` to verify only the ✅ files appear.

---

### Task 5 — Profile README

Create a special repo named exactly `YOUR_USERNAME` (same as your GitHub username):

```bash
gh repo create YOUR_USERNAME --public
```

Add a `README.md` that will appear on your GitHub profile:

```markdown
# Hi, I'm YOUR_NAME 👋

## About Me
- 🔭 Learning GitHub and Docker
- 🌱 Currently working through the GitHub Lab Session
- 💬 Ask me about Git commands!

## My Stats
- Repos: [your count]
- Today I learned: version control, branching, and CI/CD basics
```

---

## 🏆 Submission

Run this command and share the output with your instructor:

```bash
echo "=== My Lab Submission ==="
echo "GitHub Username: $(gh api user --jq '.login')"
echo "Repos created today:"
gh repo list --limit 10 --json name,createdAt \
  --jq '.[] | select(.createdAt > (now - 86400 | todate)) | .name'
```
