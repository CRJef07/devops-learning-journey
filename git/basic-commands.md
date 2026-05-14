# Git Basic Commands

## Initialize Repository

```bash
git init
```

Initializes a new Git repository in the current directory.

---

## Check Repository Status

```bash
git status
```

Shows:
- modified files
- staged files
- current branch

---

## Add Files

```bash
git add .
```

Stages all files for commit.

---

## Create Commit

```bash
git commit -m "message"
```

Creates a commit with a message.

Example:

```bash
git commit -m "Add initial Linux notes structure"
```

---

## Configure Git Username

```bash
git config --global user.name "Andres Vega"
```

---

## Configure Git Email

```bash
git config --global user.email "your@email.com"
```

---

## Rename Branch To Main

```bash
git branch -M main
```

---

## View Branches

```bash
git branch
```

---

## Add Remote Repository

```bash
git remote add origin https://github.com/USER/REPO.git
```

---

## Push To GitHub

```bash
git push -u origin main
```