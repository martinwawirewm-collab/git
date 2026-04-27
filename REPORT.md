# Git Version Control Project

## Overview

This project documents my hands-on journey learning Git — from initial setup and basic commits through to branching, merging, rebasing, and working with remote repositories. Every task is tracked via commit history to demonstrate the evolution of work.

---

## Work Directory Structure

```
work/
├── hello/                  # Main Git repository
│   ├── .git/               # Git internals (objects, refs, config, HEAD)
│   ├── lib/
│   │   ├── hello.sh        # Main shell script (moved here from root)
│   │   └── greeter.sh      # Greeter function (added in greet branch)
│   ├── Makefile            # Build/run automation
│   └── README.md           # Project readme (added during branching task)
│
└── cloned_hello/           # Cloned copy of hello repository
    ├── lib/
    │   ├── hello.sh
    │   └── greeter.sh
    ├── Makefile
    └── README.md

hello.git/                  # Bare repository (shared/remote)
```

---

## Table of Contents

1. [Setup and Installation](#1-setup-and-installation)
2. [Git Commits to Commit](#2-git-commits-to-commit)
3. [History](#3-history)
4. [Check It Out](#4-check-it-out)
5. [TAG Me](#5-tag-me)
6. [Changed Your Mind?](#6-changed-your-mind)
7. [Move It](#7-move-it)
8. [Blobs, Trees and Commits](#8-blobs-trees-and-commits)
9. [Branching](#9-branching)
10. [Conflicts, Merging and Rebasing](#10-conflicts-merging-and-rebasing)
11. [Local and Remote Repositories](#11-local-and-remote-repositories)
12. [Bare Repositories](#12-bare-repositories)
13. [Audit Q&A](#13-audit-qa)

---

## 1. Setup and Installation

### Install Git

Git was installed by following the official instructions at https://git-scm.com/downloads for the local OS.

Verified installation:
```bash
git --version
```

### Configure Git

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

Verified configuration:
```bash
git config --list
```

---

## 2. Git Commits to Commit

### Create Working Directory and hello.sh

```bash
mkdir -p work/hello
cd work/hello
```

Created `hello.sh` with initial content:
```bash
echo "Hello, World"
```

### Initialize Git Repository

```bash
git init
```

### Check Repository Status

```bash
git status
```

Output showed `hello.sh` as an untracked file.

### First Commit

```bash
git add hello.sh
git commit -m "Initial commit: Add hello.sh"
```

### Modify hello.sh (First Change)

Updated `hello.sh` to:
```bash
#!/bin/bash

echo "Hello, $1"
```

Staged and committed:
```bash
git add hello.sh
git commit -m "Add shebang and use argument variable"
```

Verified clean working tree:
```bash
git status
# Output: nothing to commit, working tree clean
```

### Add Comments — Two Separate Commits

**Stage 1:** Modified `hello.sh` to add only the comment on line 3:
```bash
#!/bin/bash

# Default is "World"
name=${1:-"World"}
echo "Hello, $name"
```

First commit (comment only):
```bash
git add hello.sh
git commit -m "Add comment on line 3"
```

**Stage 2:** Then added lines 4 and 5 changes:
```bash
git add hello.sh
git commit -m "Add default name variable and update echo"
```

---

## 3. History

### Full History

```bash
git log
```

### One-Line History

```bash
git log --oneline
```

### Controlled Entries — Last 2

```bash
git log -2
```

### Commits in the Last 5 Minutes

```bash
git log --since="5 minutes ago"
```

### Personalized Format

```bash
git log --pretty=format:"* %h %ad | %s%d [%an]" --date=short
```

Sample output:
```
* e4e3645 2023-06-10 | Added a comment (HEAD -> main) [John Doe]
```

---

## 4. Check It Out

### Restore First Snapshot

Find the first commit hash:
```bash
git log --oneline
```

Checkout the first commit:
```bash
git checkout <first-commit-hash>
cat hello.sh
```

At this point `hello.sh` contained only:
```bash
echo "Hello, World"
```

### Restore Second Most Recent Snapshot

```bash
git checkout <second-commit-hash>
cat hello.sh
```

### Return to Latest Version (main branch)

```bash
git checkout main
```

This brings the working directory to the latest version without referencing any commit hash.

---

## 5. TAG Me

### Tag Current Version as v1

```bash
git tag v1
```

### Tag Previous Version as v1-beta

```bash
git checkout v1^
git tag v1-beta
git checkout main
```

### Navigate Between Tags

```bash
git checkout v1-beta
git checkout v1
```

### List All Tags

```bash
git tag
```

---

## 6. Changed Your Mind?

### Revert Unstaged Changes

Added unwanted comment to `hello.sh`:
```bash
#!/bin/bash

# This is a bad comment. We want to revert it.
name=${1:-"World"}

echo "Hello, $name"
```

Reverted before staging:
```bash
git checkout hello.sh
```

### Stage and Clean Staging Area

Added unwanted staged comment:
```bash
#!/bin/bash

# This is an unwanted but staged comment
name=${1:-"World"}

echo "Hello, $name"
```

Staged it, then cleaned the staging area:
```bash
git add hello.sh
git restore --staged hello.sh
git checkout hello.sh
```

### Commit and Revert

Added unwanted committed change, staged, and committed:
```bash
#!/bin/bash

# This is an unwanted but committed change
name=${1:-"World"}

echo "Hello, $name"
```

```bash
git add hello.sh
git commit -m "Add unwanted change"
```

Reverted using `git revert`:
```bash
git revert HEAD --no-edit
```

### Tag Oops and Reset to v1

```bash
git tag oops
git reset --hard v1
```

### Display Logs Including Deleted Commits

```bash
git log --all --oneline
```

The commit tagged `oops` is visible even after the reset.

### Clean Unreferenced Commits

```bash
git reflog expire --expire=now --all
git gc --prune=now
```

After this, `git log --all` no longer shows the deleted commits.

### Add Author Information

Updated `hello.sh`:
```bash
#!/bin/bash

# Default is World
# Author: Jim Weirich
name=${1:-"World"}

echo "Hello, $name"
```

```bash
git add hello.sh
git commit -m "Add author comment"
```

### Amend to Include Author Email

Updated `hello.sh` to include email:
```bash
#!/bin/bash

# Default is World
# Author: Jim Weirich
# Email: jim@weirich.net
name=${1:-"World"}

echo "Hello, $name"
```

Amended the last commit (no new commit created):
```bash
git add hello.sh
git commit --amend --no-edit
```

---

## 7. Move It

### Move hello.sh into lib/

```bash
mkdir lib
git mv hello.sh lib/hello.sh
git commit -m "Move hello.sh into lib/ directory"
```

### Create and Commit Makefile

Created `Makefile` in the repository root:
```makefile
TARGET="lib/hello.sh"

run:
	bash ${TARGET}
```

```bash
git add Makefile
git commit -m "Add Makefile"
```

---

## 8. Blobs, Trees and Commits

### Exploring .git/ Directory

```bash
ls .git/
```

| Subdirectory/File | Purpose |
|---|---|
| `objects/` | Stores all Git objects: blobs (file contents), trees (directories), commits, and tags. Each is identified by a SHA-1 hash. |
| `refs/` | Contains references (pointers) to commit hashes. `refs/heads/` holds branch tips; `refs/tags/` holds tags. |
| `config` | Repository-level configuration (remote URLs, branch tracking, user settings). |
| `HEAD` | A symbolic reference pointing to the currently checked-out branch (e.g., `ref: refs/heads/main`). In detached HEAD state, it points directly to a commit hash. |
| `index` | The staging area — a binary file tracking what will go into the next commit. |
| `logs/` | Records history of all ref updates (used by `git reflog`). |

### Find Latest Object Hash

```bash
git cat-file -t $(git log --pretty=format:"%T" -1)
git cat-file -p $(git log --pretty=format:"%T" -1)
```

Or find the latest object directly:
```bash
git log -1 --format="%H"
git cat-file -t <hash>
git cat-file -p <hash>
```

### Dump Directory Tree from Commit

```bash
git ls-tree HEAD
```

### Dump Contents of lib/ and hello.sh

```bash
git ls-tree HEAD lib/
git cat-file -p HEAD:lib/hello.sh
```

---

## 9. Branching

### Create and Switch to greet Branch

```bash
git checkout -b greet
```

### Create greeter.sh in lib/

Created `lib/greeter.sh`:
```bash
#!/bin/bash

Greeter() {
    who="$1"
    echo "Hello, $who"
}
```

```bash
git add lib/greeter.sh
git commit -m "Add greeter.sh with Greeter function"
```

### Update lib/hello.sh

Updated `lib/hello.sh` to use the greeter:
```bash
#!/bin/bash

source lib/greeter.sh

name="$1"
if [ -z "$name" ]; then
    name="World"
fi

Greeter "$name"
```

```bash
git add lib/hello.sh
git commit -m "Update hello.sh to use Greeter function"
```

### Update Makefile

```makefile
# Ensure it runs the updated lib/hello.sh file
TARGET="lib/hello.sh"

run:
	bash ${TARGET}
```

```bash
git add Makefile
git commit -m "Add comment to Makefile"
```

### Compare main and greet Branches

```bash
git checkout main
git diff main greet -- Makefile
git diff main greet -- lib/hello.sh
git diff main greet -- lib/greeter.sh
```

### Generate README.md

Created `README.md`:
```
This is the Hello World example from the git project.
```

```bash
git add README.md
git commit -m "Add README.md"
```

### Commit Tree Diagram

```bash
git log --all --oneline --graph --decorate
```

Sample output:
```
* a1b2c3d (greet) Add comment to Makefile
* e4f5g6h Update hello.sh to use Greeter function
* i7j8k9l Add greeter.sh with Greeter function
| * m0n1o2p (HEAD -> main) Add README.md
|/
* q3r4s5t Move hello.sh into lib/ directory
...
```

---

## 10. Conflicts, Merging and Rebasing

### Merge main into greet

```bash
git checkout greet
git merge main
```

### Switch to main and Make Changes

```bash
git checkout main
```

Updated `lib/hello.sh`:
```bash
#!/bin/bash

echo "What's your name"
read my_name

echo "Hello, $my_name"
```

```bash
git add lib/hello.sh
git commit -m "Update hello.sh to prompt for name"
```

### Merge main into greet (Conflict)

```bash
git checkout greet
git merge main
# CONFLICT in lib/hello.sh
```

Conflict markers appeared in `lib/hello.sh`. Manually resolved by accepting the changes from `main`.

### Resolve Conflict and Commit

```bash
# Edited lib/hello.sh to resolve conflict
git add lib/hello.sh
git commit -m "Resolve merge conflict: accept changes from main"
```

### Rebase greet onto main

First, return to the state before the initial merge:
```bash
git checkout greet
git reset --hard <commit-before-first-merge>
```

Then rebase:
```bash
git rebase main
```

### Merge greet into main

```bash
git checkout main
git merge greet
```

This was a **fast-forward merge** since greet was directly ahead of main after the rebase.

---

## 11. Local and Remote Repositories

### Clone hello into cloned_hello

```bash
cd work/
git clone hello cloned_hello
```

### Show Logs for Cloned Repository

```bash
cd cloned_hello
git log --oneline
```

### Display Remote Information

```bash
git remote -v
git remote show origin
```

### List All Branches

```bash
git branch -a
```

### Update Original Repository

```bash
cd ../hello
```

Updated `README.md`:
```
This is the Hello World example from the git project.
(changed in the original)
```

```bash
git add README.md
git commit -m "Update README with change note"
```

### Fetch Changes in cloned_hello

```bash
cd ../cloned_hello
git fetch origin
git log --all --oneline
```

Commits from the `hello` repository appeared in the logs.

### Merge Remote Changes

```bash
git merge origin/main
```

### Add Local greet Branch Tracking Remote

```bash
git checkout --track origin/greet
```

### Add Remote and Push

```bash
git remote add origin https://github.com/username/hello.git
git push origin main
git push origin greet
```

---

## 12. Bare Repositories

### Create Bare Repository

```bash
cd work/
git clone --bare hello hello.git
```

### Add Bare Repo as Remote to hello

```bash
cd hello
git remote add shared ../hello.git
```

### Update README.md, Commit, and Push

Updated `README.md`:
```
This is the Hello World example from the git project.
(Changed in the original and pushed to shared)
```

```bash
git add README.md
git commit -m "Update README for shared push"
git push shared main
```

### Pull Changes in cloned_hello

```bash
cd ../cloned_hello
git remote add shared ../hello.git
git pull shared main
```

---

## 13. Audit Q&A

### What is the single git command to bring remote changes to local main?

```bash
git pull
```

`git pull` is equivalent to running `git fetch` followed by `git merge origin/main`. It fetches all changes from the remote and merges them into the current local branch in one step.

---

### Explain the .git/ Subdirectories

| Path | Purpose |
|---|---|
| `objects/` | The object database. Every file version, directory snapshot, and commit is stored here as a compressed object, keyed by its SHA-1 hash. There are four types: **blob** (file content), **tree** (directory listing), **commit** (snapshot + metadata), **tag** (named reference). |
| `refs/` | Human-readable pointers to commit hashes. `refs/heads/` contains branch pointers; `refs/tags/` contains tag pointers; `refs/remotes/` contains remote tracking branches. |
| `config` | Local configuration for this repository only — remote URLs, branch upstream tracking, and any repo-level overrides to global config. |
| `HEAD` | Indicates what is currently checked out. Normally a symbolic ref like `ref: refs/heads/main`. In detached HEAD state (when checking out a tag or raw commit), it holds the commit hash directly. |

---

### What is a Bare Repository and Why is it Needed?

A **bare repository** is a Git repository that contains only the version history data (the contents of `.git/`) with no working directory. It is created using:

```bash
git clone --bare <repo> <name>.git
```

**Why it is needed:**
- It is used as a **shared/central repository** that multiple people push to and pull from.
- A regular (non-bare) repository refuses pushes to its checked-out branch because that would corrupt the working directory. A bare repo has no working tree, so pushes are always safe.
- Bare repositories are what services like GitHub, GitLab, and Gitea serve behind the scenes.

---

### Explain the Difference Between Merging and Rebasing

| | Merging | Rebasing |
|---|---|---|
| **What it does** | Combines two branch histories by creating a new merge commit | Moves your branch's commits on top of another branch, rewriting history |
| **History** | Preserves the full history with a merge commit showing where branches joined | Creates a linear, cleaner history as if work was done in sequence |
| **Commit hashes** | Original commits unchanged | Commits are rewritten — new SHA-1 hashes |
| **Use case** | Integrating long-lived branches (e.g. `feature` → `main`) | Keeping a feature branch up to date with `main` before merging |
| **Conflicts** | Resolved once at the merge point | May need to resolve conflicts at each replayed commit |
| **Safe for shared branches?** | Yes | **No** — never rebase branches others are working on |

**Fast-Forwarding:**

A fast-forward merge occurs when the target branch has no new commits since the source branch diverged — meaning the source is simply ahead of the target in a straight line. Instead of creating a merge commit, Git just moves the branch pointer forward.

```bash
# Before: main is at commit A, greet is at A → B → C
git checkout main
git merge greet
# After: main pointer moves to C (no merge commit created)
```

You can prevent fast-forwarding to always create a merge commit:
```bash
git merge --no-ff greet
```

---

## Challenges and Lessons Learned

**Challenge 1: Two separate commits for one file**
Making two separate commits for different parts of the same file required careful staging. Used `git add -p` (interactive patch staging) to stage only specific lines at a time.

**Challenge 2: Detached HEAD state**
When checking out old commits and tags, Git enters a detached HEAD state. Learned that you must run `git checkout main` (or the branch name) to return to normal — not just `git checkout HEAD`.

**Challenge 3: Resolving merge conflicts**
The conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) had to be manually removed and the correct version selected. Used a text editor to carefully resolve the conflict before staging and committing.

**Challenge 4: Rebase vs. Merge**
Rebase rewrites history, so it required going back to the pre-merge state with `git reset --hard` before rebasing. Understood that rebasing is cleaner but should never be done on shared/public branches.

**Challenge 5: Cleaning unreferenced commits**
After `git reset --hard`, the old commits still showed in `git log --all` via the reflog. Had to expire the reflog and run `git gc --prune=now` to fully remove them.

---

## Repository

GitHub/Gitea Repo: [https://learn.zone01kisumu.ke/git/mwawire/git](https://learn.zone01kisumu.ke/git/mwawire/git)