# Git Commands Reference

Common Git commands and workflows used during daily development, infrastructure management, automation, and DevOps projects.

This document focuses on practical Git usage following industry best practices commonly used by Linux Administrators, DevOps Engineers, SREs, and Cloud Engineers.

---

## Git Workflow Overview

Git works using 5 primary areas:

```text
Working Directory
(Untracked & modified files on disk)
        │
        │  git add <file>
        ▼
Staging Area (Index)
(Snapshot of what will go into next commit)
        │
        │  git commit -m "msg"
        ▼
Local Repository
(Full history in .git — branches, tags, objects)
        │
        │  git push
        ▼
Remote Repository
(GitHub / GitLab / Bitbucket)
        │
        │  git fetch / git pull
        ▼
Local Repository  ← (cycle repeats)
```

```text
Stash  (side channel — not committed, not lost)
    git stash      → saves working directory changes temporarily
    git stash pop  → restores them back to working directory
```

Understanding this workflow is critical for troubleshooting Git issues and maintaining a
clean commit history.

---

## Git Configuration

> Configure Git before doing anything else. These settings are permanently recorded in
> every commit you create.

### Configure Username

```bash
git config --global user.name "Your Name"
```

Sets the global Git username.

### Configure Email

```bash
git config --global user.email "you@example.com"
```

Sets the global Git email address.

### Configure Default Branch

```bash
git config --global init.defaultBranch main
```

Sets `main` as the default branch name for all new repositories.
Aligns with GitHub, GitLab, and most enterprise standards.

### Configure Default Editor

```bash
# Visual Studio Code
git config --global core.editor "code --wait"

# Vim (common on remote servers)
git config --global core.editor "vim"

# Nano (simpler alternative)
git config --global core.editor "nano"
```

### Verify Full Configuration

```bash
git config --list
```

Lists all active configuration values and the file they come from.
Useful when diagnosing unexpected behaviour on a new machine or in a CI environment.

---

## Repository Initialization

```bash
git init
```

Creates a `.git/` directory in the current folder, turning it into a Git repository.

---

## Repository Cloning

```bash
git clone https://github.com/USER/REPO.git
```

Creates a full local copy of a remote repository, including its complete history.

---

## Inspecting State

Run these commands before staging or committing. They prevent most common mistakes.

### Check Repository Status

```bash
git status
```

Shows:

- current branch
- modified files (not yet staged)
- staged files (ready to commit)
- untracked files (Git has never seen them)

Useful for understanding the current state of the working directory and staging area.

### Compare Working Directory vs Staging Area

```bash
git diff
```

Shows changes you have made but **not yet staged**.

Run this before `git add` to review exactly what you are about to stage.

### Compare Staging Area vs Last Commit

```bash
git diff --cached
```

Shows changes that **are staged** and will go into the next commit.

Run this after `git add` and before `git commit` to verify the snapshot is correct.

---

## Staging Changes

Git uses a staging area (also called the index) between the working directory and the commit history. Use it deliberately — it is where commit quality is decided.

### Stage a Specific File

```bash
git add README.md
```

Stages only the specified file.

Useful for creating clean, focused commits.

### Stage Specific Chunks Inside a File (Patch Mode)

```bash
git add -p
```

Walks through each changed section (hunk) interactively, letting you choose which
individual chunks to stage instead of staging the entire file.

Very useful when:

- separating unrelated changes into distinct commits
- reviewing every modification carefully before committing
- splitting one long work session into multiple clean commits

Senior engineers use patch mode as their default staging method. It is the single most
effective habit for maintaining a high-quality commit history.

### Stage All Changes in Current Directory

```bash
git add .
```

Stages all modified and new files inside the current directory and its subdirectories.

This command is **path-relative** — if run from a subdirectory, only files inside that
subtree are staged. This distinction matters in monorepos and scripts. Use `git add -A`
when you need to guarantee the entire repository is covered.

### Stage All Changes Across the Entire Repository

```bash
git add -A
```

or

```bash
git add --all
```

Stages new, modified, and **deleted** files across the **entire repository**, regardless
of your current working directory.

Recommended in infrastructure and DevOps repositories because it guarantees deleted files
(removed configs, retired scripts) are tracked correctly.

---

## Commits

### Create a Commit

```bash
git commit -m "message"
```

Records staged changes as a permanent snapshot in local history.

#### Commit Message Convention

Write messages in the **imperative mood** — as if completing the sentence
"This commit will…":

```bash
# Good — imperative, specific, under 72 characters
git commit -m "Add SSH troubleshooting notes"
git commit -m "Document Linux file permissions"
git commit -m "Add Docker installation guide"
git commit -m "Remove deprecated Ansible playbook"

# Poor — vague, past tense, or too broad
git commit -m "changes"
git commit -m "fixed stuff"
git commit -m "update"
```

For complex changes, open your editor to add a subject line and a body:

```bash
git commit
```

```text
Add rate limiting to nginx reverse proxy config

Without rate limiting, the proxy was vulnerable to request floods that could
exhaust upstream application threads. This adds a 10r/s zone with a burst
of 20 requests. Tested in staging against simulated load.
```

The subject line answers **what**. The body answers **why**.

### View Commit History

```bash
git log
```

Displays detailed commit history with author, date, and message.

### View Compact Commit History

```bash
git log --oneline
```

Displays a shorter, cleaner commit history — one line per commit.

Example:

```text
dfa70e3 Add SSH troubleshooting notes
8b1c3a1 Document Linux file permissions
3e9f201 Add Docker installation guide
```

### View History With Branch Graph

```bash
git log --oneline --graph --decorate --all
```

Shows the same compact history with a visual branch graph and branch/tag labels.
Essential when working with multiple branches.

Example:

```text
* dfa70e3 (HEAD -> main, origin/main) Add SSH troubleshooting notes
* 8b1c3a1 Document Linux file permissions
* 3e9f201 Add Docker installation guide
```

---

## Undoing Changes

Understanding the risk level of each undo operation is critical. Some are safe and
reversible; others permanently destroy work.

### Discard Changes in Working Directory ⚠ Destructive

```bash
git restore <file>
```

Discards all unstaged modifications to the file and restores it to the last staged or
committed state.

> **Warning:** this is permanent. Git has no recycle bin for working directory changes.
> Always run `git diff` first to see exactly what you are about to lose.

### Unstage a File (Safe)

```bash
git restore --staged <file>
```

Moves the file back out of the staging area into modified state.
Your working directory changes are completely untouched.

### Undo Last Commit — Keep Changes Staged (Safe)

```bash
git reset HEAD~1 --soft
```

Moves HEAD back one commit. Changes stay staged. Working directory is untouched.

Useful for rewriting a commit message or splitting a commit before pushing.

### Undo Last Commit — Keep Changes Unstaged (Safe)

```bash
git reset HEAD~1
```

Moves HEAD back one commit. Changes return to the working directory as unstaged.

### Undo Last Commit and Wipe All Changes ⚠ Destructive

```bash
git reset HEAD~1 --hard
```

Moves HEAD back one commit **and** permanently deletes all changes in both the staging
area and working directory.

> **Warning:** data destroyed by `--hard` cannot be recovered unless you have a stash
> or a remote backup. Never run this on a shared branch.

---

## Stash

The stash is a temporary shelf for saving incomplete work so you can switch context
without losing changes — not committed, not lost.

### Save Current Changes to Stash

```bash
git stash
```

### Save with a Descriptive Label (Recommended)

```bash
git stash push -m "WIP: nginx rate limiting experiment"
```

Always name your stashes. The default label is just the last commit hash, which becomes
meaningless after a few days.

### List Stashes

```bash
git stash list
```

### Restore the Most Recent Stash and Remove It

```bash
git stash pop
```

### Restore a Specific Stash Without Removing It

```bash
git stash apply stash@{2}
```

### Drop a Stash

```bash
git stash drop stash@{0}
```

> The stash is **not** long-term storage. It has no remote backup and is not visible to
> teammates. Use it for minutes or hours, not days.

---

## Branch Management

### Create and Switch to a New Branch

```bash
git switch -c feature/add-nginx-notes
```

Always branch off before starting new work. Never work directly on `main`.

### Switch to an Existing Branch

```bash
git switch main
```

### View Local Branches

```bash
git branch
```

Lists all local branches.

### View All Branches Including Remote-Tracking

```bash
git branch -a
```

### Rename Current Branch to Main

```bash
git branch -M main
```

Renames the current branch to `main`.

Typically used once after `git init` to rename the default `master` to `main`.

### Delete a Merged Branch

```bash
git branch -d feature/add-nginx-notes
```

---

## Remote Repositories

### Add Remote Repository

```bash
git remote add origin https://github.com/USER/REPO.git
```

Connects the local repository to a remote. `origin` is the conventional name for the primary remote.

### View Remote Repositories

```bash
git remote -v
```

Displays configured remote repositories and their URLs.

Example output:

```text
origin  https://github.com/USER/REPO.git (fetch)
origin  https://github.com/USER/REPO.git (push)
```

---

## Push and Pull Operations

### Push — First Time (Sets Upstream)

```bash
git push -u origin main
```

The `-u` (`--set-upstream`) flag links the local branch to the remote branch.
After running this once, future pushes only need `git push`.

### Push — Regular (After Upstream Is Set)

```bash
git push
```

Uploads local commits to the remote using the existing upstream configuration.

> **Never force-push to `main` or any shared branch.** Force-pushing rewrites history for everyone on the team.

### Fetch Without Merging (Recommended)

```bash
git fetch origin
```

Downloads new commits and branch updates from the remote **without** touching your
working directory or current branch.

Prefer `git fetch` over `git pull` on shared branches — inspect before merging:

```bash
git log HEAD..origin/main --oneline   # see what is incoming
git diff HEAD origin/main             # see the actual diff
```

Then merge deliberately.

### Pull — Fetch and Merge in One Step

```bash
git pull
```

Downloads and merges changes from the remote repository into the current branch.

- Recommended before starting new work
- Fine for personal branches
- On shared branches, prefer `git fetch` first so you can inspect what is incoming

---

## Useful Notes

### .gitignore

A `.gitignore` file tells Git which files and directories to never track.

#### Common Patterns

```gitignore
# OS files
.DS_Store
Thumbs.db

# Editor and IDE files
.vscode/
.idea/
*.swp

# Secrets and credentials — never commit these
.env
*.pem
*.key
id_rsa
secrets.yaml

# Compiled output and dependencies
node_modules/
__pycache__/
*.pyc
dist/
build/

# Logs
*.log
logs/
```

> Secrets committed to Git history remain there **forever**, even after being deleted from HEAD.
> If you accidentally commit credentials, rotate them immediately, deleting the file is not enough.

---

## Recommended Best Practices

| Practice | Why it matters |
| --- | --- |
| Configure identity before first commit | Every commit is permanently signed with your name and email |
| Run `git status` and `git diff` before staging | Prevents accidentally staging debug code, secrets, or unrelated changes |
| Use `git add -p` for complex sessions | Keeps commits focused and makes history readable |
| Write imperative commit messages | Consistent, readable history that aligns with `git revert` output |
| One logical change per commit | Easier to revert, review, and bisect in production incidents |
| Never commit secrets or credentials | They cannot be fully erased from history — treat it as a security incident |
| Add a `.gitignore` from day one | Prevents OS noise, editor files, and secrets from entering the repo |
| `git fetch` before `git pull` on shared branches | Lets you inspect incoming changes before they touch your local state |
| Never force-push to `main` or shared branches | Rewrites history for the entire team |
| Pull before starting new work | Avoids diverging unnecessarily from the remote |
