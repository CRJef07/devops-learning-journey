# First-Time Repository Setup and Daily Workflow

Common Git workflows used during daily development and infrastructure projects.

## First-Time Repository Setup

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin <repo-url>
git push -u origin main
```

The `-u` flag sets the upstream tracking branch, allowing future pushes using only:

```bash
git push
```

---

## Typical Daily Git Workflow

### Update Local Repository with the Latest Changes

```bash
git pull
```

Downloads and merges the latest changes from the remote repository.

---

### Check repository changes

```bash
git status
```

Shows:

- modified files
- staged files
- current branch

---

### Add modified files

```bash
git add .
```

Or add a specific file:

```bash
git add README.md
```

---

### Create commit

```bash
git commit -m "Describe your changes"
```

Example:

```bash
git commit -m "Add Linux networking notes"
```

---

### Push changes to GitHub

```bash
git push
```

---

## Example Daily Workflow

```bash
git pull
git status
git add .
git commit -m "Add Linux storage notes"
git push
```

---

## View Commit History

```bash
git log
```

Compact version:

```bash
git log --oneline
```

---

## Clone Existing Repository

```bash
git clone https://github.com/USER/REPO.git
```

---

## View Remote Repository

```bash
git remote -v
```

Shows configured remote repositories.

---

## Git Workflow Overview

```text
Working Directory
        ↓
git add
        ↓
Staging Area
        ↓
git commit
        ↓
Local Repository
        ↓
git push
        ↓
GitHub Remote Repository
```

## Useful Best Practices

- Commit frequently
- Use meaningful commit messages
- Push changes regularly
- Pull before pushing changes
- Keep repositories organized
- Never commit secrets/passwords
- Use `.gitignore`

---

## Recommended Commit Message Examples

```bash
git commit -m "Add SSH notes"
```

```bash
git commit -m "Document Linux permissions"
```

```bash
git commit -m "Add Docker installation steps"
```
