Git Version Control Project

A hands-on project covering Git fundamentals through to advanced workflows including branching, merging, rebasing, and remote repositories.

Repository
https://learn.zone01kisumu.ke/git/mwawire/git

Work Directory Structure
work/
├── hello/              # Main Git repository
│   ├── lib/
│   │   ├── hello.sh
│   │   └── greeter.sh
│   ├── Makefile
│   └── README.md
├── cloned_hello/       # Cloned repository
└── hello.git/          # Bare (shared) repository

Sections Covered
1. Setup
Configured username and email using git config --global.

2. Git Commits
Created hello/ directory, initialized a repo, and progressively modified hello.sh across multiple commits — practicing staging, committing, and keeping a clean working tree.

3. History
Explored git log with flags: --oneline, -2, --since, and a custom --pretty=format to display commit hash, date, message, branch, and author.

4. Check It Out
Used git checkout <hash> to navigate between snapshots and git checkout main to return to the latest version.

5. Tagging
Tagged the current version as v1 and the previous as v1-beta using git tag. Navigated between them and listed all tags.

6. Undoing Changes
Practiced reverting unstaged changes (git checkout), cleaning the staging area (git restore --staged), reverting commits (git revert), and hard resetting to a tagged version (git reset --hard v1). Also used git commit --amend to update the last commit without creating a new one.

7. Moving Files
Moved hello.sh into lib/ using git mv and added a Makefile to the repo root.

8. Git Internals
Explored the .git/ directory — understanding objects/, refs/, config, and HEAD. Used git cat-file and git ls-tree to inspect objects directly.

9. Branching
Created a greet branch, added greeter.sh, updated hello.sh and Makefile, then compared branches with git diff. Added README.md on main and drew the diverging commit tree with git log --all --graph.

10. Merging & Rebasing
Merged main into greet, then intentionally triggered a conflict by modifying hello.sh on main. Resolved the conflict manually, then went back and redid the workflow using git rebase instead. Finally merged greet into main.

11. Remote Repositories
Cloned hello as cloned_hello, inspected remotes, fetched and merged changes, tracked the remote greet branch, and pushed both branches to a remote.

12. Bare Repositories
Created hello.git as a bare repo using git clone --bare, added it as a remote, pushed changes to it, and pulled them down from cloned_hello.

Key Concepts
Merging vs Rebasing — Merging preserves full history with a merge commit. Rebasing replays commits on top of another branch for a cleaner, linear history. Rebasing rewrites commit hashes so it should never be done on shared branches.
Fast-forwarding — When no diverging commits exist, Git simply moves the branch pointer forward instead of creating a merge commit.
Bare Repository — A repo with no working directory, used as a shared central remote that safely accepts pushes from multiple contributors.
git pull — Equivalent to git fetch followed by git merge origin/main.

Challenges
Two commits for one file — Staging only specific lines of hello.sh for separate commits required using git add -p to interactively select hunks rather than staging the whole file at once.
Detached HEAD state — Checking out old commits and tags left the repo in detached HEAD state. Had to remember to run git checkout main to get back rather than assuming HEAD would follow automatically.
Resolving merge conflicts — When merging main into greet, both branches had modified hello.sh differently. Manually removing conflict markers and selecting the correct version was tricky at first.
Rebase vs. reset — Before rebasing, the repo had to be rolled back to the pre-merge state using git reset --hard. Understanding that rebase rewrites history (new commit hashes) and should not be used on shared branches was an important distinction to grasp.
Cleaning unreferenced commits — After git reset --hard, old commits still appeared via the reflog. Fully removing them required running git reflog expire --expire=now --all followed by git gc --prune=now.