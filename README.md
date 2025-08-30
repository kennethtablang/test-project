# Git Commands — Reviewer (clear definitions → examples → tips)

---

## Setup & Identity

**What it is:** Configure Git once so commits have your name/email and Git behaves how you like.

```bash
# Set your user identity (do this once per machine)
git config --global user.name "Kenneth Tablang"
git config --global user.email "you@example.com"

# See effective config
git config --list
```

**Tip:** Use `--global` for machine-wide settings; omit `--global` if you want repo-specific settings.

---

## Create / Clone Repos

**What it is:** Start a new repo locally or copy an existing remote repo to your machine.

```bash
# Initialize a new repository in this folder
git init

# Clone an existing repo from GitHub
git clone https://github.com/username/repo.git
# or using SSH
git clone git@github.com:username/repo.git
```

**When to use:** `git init` for brand-new projects. `git clone` when you need an existing repo.

---

## Inspecting State

**What it is:** Check what changed, which branch you’re on, and view commit history.

```bash
git status                 # current files staged/unstaged/untracked
git branch                 # list local branches, * marks current
git branch -a              # list local + remote branches
git log --oneline --graph  # compact commit history graph
git diff                   # show unstaged changes (working directory vs index)
git show HEAD              # show last commit details
```

**Tip:** `git log --oneline --graph --all` is great for visualizing branch merges.

---

## Staging & Committing (local save points)

**What it is:** Stage changes and create atomic commit snapshots.

```bash
git add file.txt           # stage a file
git add .                  # stage all changes (new/modified)
git commit -m "Short message"        # commit staged changes
git commit -am "Msg"       # shortcut: add+commit for tracked files only
```

**Best practice:** Make small, frequent commits with clear messages.

---

## Syncing with Remote (push/pull/fetch)

**What it is:** Send your commits to GitHub (push) and bring remote commits to your machine (fetch/pull).

```bash
# First time pushing a new branch and set upstream
git push -u origin branch-name

# Push commits (after upstream is set)
git push

# Get remote updates (fetch only updates refs)
git fetch --all

# Fetch + merge remote changes into the current branch
git pull        # equivalent to `git fetch` + `git merge` by default
```

**Note:** Use `git fetch` when you want to see remote updates without merging. Use `git pull` to update your branch directly.

---

## Branching & Switching

**What it is:** Branches let you work independently on features without touching `main`.

```bash
# Create a branch (local only)
git branch feature-xyz

# Create and switch to it (single step)
git checkout -b feature-xyz
# or (newer)
git switch -c feature-xyz

# Switch to an existing branch
git checkout main
# or
git switch main

# Create local branch tracking remote branch
git checkout -b intro origin/intro
# or
git switch -c intro --track origin/intro
```

**Tip:** Use `git switch` for switching and `git checkout -b` for older Git versions.

---

## Merging & Pull Requests

**What it is:** Combine changes from one branch into another (often `intro` → `main`).

```bash
# On main, merge intro into main
git checkout main
git fetch --all
git merge intro

# If merge finishes, push main
git push origin main
```

**If conflicts occur:** Git will mark conflicted files. Open those files (they’ll have `<<<<<<< HEAD` and `>>>>>>> intro` markers). Edit them manually, save, then run:

```bash
git add <conflicted-file>
git commit            # finalizes merge commit
git push
```

**Alternative workflow:** Create a **Pull Request** on GitHub to merge branches with code review.

---

## Stash (temporary save) — safe rescue for WIP

**What it is:** Save uncommitted changes temporarily so you can switch branches cleanly.

```bash
git stash               # stash working changes
git stash list          # list stashes
git stash pop           # apply latest stash and remove it
git stash apply stash@{1}   # apply a specific stash without removing
git stash drop stash@{1}    # delete a stash
```

**Use case:** You started working on `main` but need to switch to `intro`. `git stash` → `git switch intro` → `git stash pop`.

---

## Undoing & Rewriting History (be careful)

**What it is:** Ways to undo things. Use with caution, especially with shared branches.

```bash
# Undo unstaged changes in a file
git checkout -- file.txt   # (older) or
git restore file.txt       # modern command

# Unstage a staged file
git reset HEAD file.txt

# Amend last commit (change message or add staged changes)
git commit --amend -m "New message"

# Revert a commit by creating a new commit (safe on shared branches)
git revert <commit-hash>

# Reset branch pointer (dangerous on shared branches; rewrites history)
git reset --hard <commit-hash>
```

**Rule of thumb:** Use `git revert` for public/shared history; `git reset --hard` only for local or when you understand consequences.

---

## Tags (release markers)

**What it is:** Mark specific commits as releases or important points.

```bash
git tag v1.0.0            # lightweight tag on current commit
git tag -a v1.0.0 -m "Release 1.0"   # annotated tag
git push origin v1.0.0
git push --tags           # push all tags
```

---

## Remote Management

**What it is:** Add, remove, and inspect remotes (e.g., `origin`).

```bash
git remote -v             # show remotes and URLs
git remote add upstream https://...
git remote remove upstream
```

---

## Useful Diagnostics

```bash
git reflog                # all branch/head movements (very helpful to recover)
git bisect                # binary search to find a bad commit
git blame file.txt        # who changed each line and when
```

---

## Common Short Workflows (cheat flows)

### Create feature, commit, push

```bash
git checkout -b feature-abc
# edit files...
git add .
git commit -m "Start feature abc"
git push -u origin feature-abc
```

### Update local branch with remote main before merging

```bash
git checkout main
git pull origin main
git checkout feature-abc
git rebase main        # (or `git merge main`) — rebase rewrites history to apply your work on top
# resolve conflicts if any, then:
git push --force-with-lease origin feature-abc   # if you rebased and need to update remote
```

### Merge a feature into main (safe, simple)

```bash
git checkout main
git pull origin main
git merge feature-abc
git push origin main
```

---

## Troubleshooting: common issues & fixes

* **Push rejected (non-fast-forward):**

  ```bash
  git pull --rebase
  # resolve conflicts, then git push
  ```
* **Forgot to switch branches and committed to main:**

  ```bash
  git checkout -b feature-from-main   # creates branch from current commit
  # Then undo the accidental commit on main (if needed)
  git checkout main
  ```

git reset --hard origin/main

````
- **Accidentally deleted a branch:**
```bash
git reflog                      # find commit where branch pointed
git branch restore-branch <commit-hash>
````

---

## Best Practices (short & powerful)

* Commit often with meaningful messages.
* Use feature branches (`feature/`, `bugfix/`, `hotfix/`) — keep `main` deployable.
* Pull before you push to minimize conflicts.
* Prefer `git revert` for fixing commits on shared branches.
* Protect `main` in GitHub (require PR reviews) when working in teams.
* Keep README + CONTRIBUTING files to describe how to work with the repo.

---

## Printable Cheat Sheet (quick reference)

```
# Setup
git config --global user.name "Name"
git config --global user.email "email"

# Start / Clone
git init
git clone <url>

# Work cycle
git status
git add <file> | git add .
git commit -m "msg"
git push -u origin <branch>
git pull

# Branching
git branch
git checkout -b <branch>    # create & switch
git switch <branch>         # switch
git merge <branch>          # merge into current

# Stash / recovery
git stash
git stash pop
git reflog

# Undo
git restore <file>
git reset --soft HEAD~1
git revert <commit>

# Remote
git fetch --all
git branch -a
git push --tags
```

---
