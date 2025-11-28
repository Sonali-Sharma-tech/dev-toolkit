# Git Concepts & Hidden Knowledge

Things they don't teach you in Git tutorials. Understanding these will make you a Git power user.

## Table of Contents
- [How Git Actually Works](#how-git-actually-works)
- [The Three Trees](#the-three-trees)
- [Lesser-Known Behaviors](#lesser-known-behaviors)
- [Common Gotchas](#common-gotchas)
- [Advanced Concepts](#advanced-concepts)
- [Recovery & Safety](#recovery--safety)
- [Performance & Optimization](#performance--optimization)

---

## How Git Actually Works

### Git is a Content-Addressable Filesystem

Git doesn't store "changes" or "diffs". It stores **snapshots** of your entire project.

```
Commit 1: [snapshot of all files at this point]
Commit 2: [snapshot of all files at this point]
Commit 3: [snapshot of all files at this point]
```

Each commit is a complete picture, not a list of changes.

### Everything is a Hash

Every object in Git (commits, files, trees) is identified by a SHA-1 hash:

```bash
git log --oneline
# a1b2c3d Fix login bug      ← a1b2c3d is a hash
# e4f5g6h Add user model
```

The hash is computed from the content. Same content = same hash, always.

```bash
# See the hash of a file
git hash-object README.md
# d670460b4b4aece5915caf5c68d12f560a9fe3e4
```

### The .git Folder is Everything

Your entire repo history lives in `.git/`:

```
.git/
├── HEAD              # Points to current branch
├── config            # Repo-specific settings
├── objects/          # All commits, files, trees (the database)
├── refs/
│   ├── heads/        # Branch pointers
│   └── tags/         # Tag pointers
├── index             # Staging area
└── hooks/            # Scripts that run on events
```

**Delete `.git/` = delete all history.** The working files remain, but Git forgets everything.

---

## The Three Trees

Git has three "trees" (areas) you work with:

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Working Dir    │────▶│  Staging Area   │────▶│   Repository    │
│  (your files)   │     │  (index)        │     │   (.git)        │
└─────────────────┘     └─────────────────┘     └─────────────────┘
        │                       │                       │
    "modified"              "staged"               "committed"
        │                       │                       │
        └───── git add ────────▶│                       │
                                └───── git commit ─────▶│
```

### Working Directory
The actual files you see and edit.

### Staging Area (Index)
A "draft" of your next commit. You choose what goes in.

```bash
git add file1.txt           # Add to staging
git reset file1.txt         # Remove from staging (keep changes)
git diff --staged           # See what's staged
```

### Repository
The committed history. Permanent (mostly).

### Why Staging Exists

You can commit **part** of your changes:

```bash
# You modified 5 files, but only want to commit 2
git add file1.txt file2.txt
git commit -m "Feature A"

# Commit the rest separately
git add file3.txt file4.txt file5.txt
git commit -m "Feature B"
```

---

## Lesser-Known Behaviors

### 1. Git Doesn't Track Empty Folders

```bash
mkdir empty_folder
git add .
git status
# nothing to commit
```

**Solution:** Add `.gitkeep` placeholder file.

### 2. Git Tracks Content, Not Files

If two files have identical content, Git stores it only once.

```bash
cp large_file.txt duplicate.txt
git add .
# Only one copy stored internally (same hash)
```

### 3. Renaming is Delete + Add

Git doesn't have a "rename" operation. It detects renames by comparing content.

```bash
git mv old.txt new.txt
# Equivalent to:
# rm old.txt
# mv old.txt new.txt
# git add new.txt
```

Git shows it as a rename only if content similarity is >50%.

### 4. File Permissions Are Tracked (Partially)

Git only tracks the **executable bit**:

```bash
chmod +x script.sh
git diff
# mode change 100644 => 100755
```

Other permissions (read/write) are ignored.

To disable permission tracking:
```bash
git config core.fileMode false
```

### 5. Line Endings Can Cause Problems

Windows uses `CRLF` (`\r\n`), Unix uses `LF` (`\n`).

```bash
# Auto-convert on Windows
git config --global core.autocrlf true

# Keep LF everywhere (recommended for cross-platform)
git config --global core.autocrlf input
```

Or use `.gitattributes`:
```
* text=auto
*.sh text eol=lf
*.bat text eol=crlf
```

### 6. Commits Are Immutable

You can't actually "edit" a commit. Commands like `--amend` and `rebase` create **new commits** and move pointers.

```bash
git commit --amend
# Doesn't edit the old commit
# Creates a NEW commit with a NEW hash
# Moves branch pointer to new commit
# Old commit still exists (until garbage collected)
```

### 7. Deleted Commits Aren't Really Deleted

"Deleted" commits exist for ~30 days in reflog:

```bash
git reflog
# Shows ALL recent HEAD positions, even "deleted" commits

git checkout abc123
# Recover a "lost" commit
```

### 8. Branches Are Just Pointers

A branch is a 41-byte file containing a commit hash:

```bash
cat .git/refs/heads/main
# a1b2c3d4e5f6g7h8i9j0...
```

Creating a branch is instant (just creates a tiny file).

### 9. HEAD is a Pointer to a Pointer

`HEAD` usually points to a branch, which points to a commit:

```
HEAD → main → abc123 (commit)
```

"Detached HEAD" means HEAD points directly to a commit:
```
HEAD → abc123 (commit)
```

### 10. Tags vs Branches

Both are pointers to commits, but:

| Branches | Tags |
|----------|------|
| Move with new commits | Stay fixed forever |
| For development | For releases (v1.0.0) |
| `refs/heads/` | `refs/tags/` |

---

## Common Gotchas

### 1. `.gitignore` Doesn't Untrack Files

If a file is already tracked, adding it to `.gitignore` does nothing.

```bash
# File already committed
echo "secret.txt" >> .gitignore
git status
# secret.txt still tracked!

# Fix: untrack it first
git rm --cached secret.txt
git commit -m "Stop tracking secret.txt"
# Now .gitignore works
```

### 2. `git pull` = `git fetch` + `git merge`

```bash
git pull origin main
# Same as:
git fetch origin main
git merge origin/main
```

Prefer `fetch` + `merge` separately for more control:
```bash
git fetch origin
git log origin/main          # See what's new
git merge origin/main        # Merge when ready
```

Or use rebase:
```bash
git pull --rebase origin main
```

### 3. Detached HEAD State

Happens when you checkout a commit (not a branch):

```bash
git checkout abc123
# You are in 'detached HEAD' state...
```

**Problem:** New commits won't belong to any branch.

**Fix:**
```bash
git checkout -b new-branch    # Create branch from here
# Or
git checkout main             # Go back to a branch
```

### 4. Force Push Overwrites History

```bash
git push --force
# Overwrites remote with YOUR version
# Other people's commits can be LOST
```

Safer alternative:
```bash
git push --force-with-lease
# Fails if remote has commits you don't have
```

### 5. Reset vs Revert vs Checkout

| Command | Does What | Use When |
|---------|-----------|----------|
| `git reset` | Moves branch pointer back | Undo local commits (not pushed) |
| `git revert` | Creates new commit that undoes | Undo pushed commits (safe) |
| `git checkout` | Switches branches or restores files | Navigate or discard changes |

```bash
# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1

# Undo a pushed commit (safe)
git revert abc123
```

### 6. Merge vs Rebase

```bash
# Merge: creates a merge commit
git merge feature
#    main: A---B---C---M
#                     /
# feature:       D---E

# Rebase: replays commits on top
git rebase main
#    main: A---B---C
#                   \
# feature:           D'---E'
```

| Merge | Rebase |
|-------|--------|
| Preserves history | Linear history |
| Creates merge commit | No merge commit |
| Safe for shared branches | Never rebase shared branches |

### 7. Stash is a Stack

```bash
git stash                     # Push to stack
git stash                     # Push another
git stash list                # See all stashes
# stash@{0}: WIP on main...
# stash@{1}: WIP on main...

git stash pop                 # Pop latest (stash@{0})
git stash pop stash@{1}       # Pop specific one
```

Stashes can get "lost" if you forget about them:
```bash
git stash list                # Check periodically!
```

---

## Advanced Concepts

### 1. Reflog - Your Safety Net

Every HEAD movement is logged:

```bash
git reflog
# abc123 HEAD@{0}: commit: Add feature
# def456 HEAD@{1}: checkout: moving from main to feature
# ghi789 HEAD@{2}: reset: moving to HEAD~3
```

Recover from almost any mistake:
```bash
git reset --hard HEAD@{2}     # Go back to that state
```

### 2. Cherry-Pick

Copy a specific commit to current branch:

```bash
git cherry-pick abc123
# Copies commit abc123 to current branch
```

Useful for:
- Applying a hotfix from one branch to another
- Pulling specific features without merging everything

### 3. Bisect - Find Bug Introduction

Binary search through commits to find when a bug was introduced:

```bash
git bisect start
git bisect bad                  # Current commit is bad
git bisect good v1.0.0          # This version was good

# Git checks out middle commit
# You test it, then:
git bisect good                 # or
git bisect bad

# Repeat until Git finds the first bad commit
git bisect reset                # Exit bisect
```

### 4. Worktrees - Multiple Working Directories

Work on multiple branches simultaneously:

```bash
git worktree add ../hotfix hotfix-branch
# Creates new folder with hotfix-branch checked out

cd ../hotfix
# Work here without switching branches

git worktree remove ../hotfix   # Clean up
```

### 5. Partial Staging (Hunks)

Stage part of a file:

```bash
git add -p file.txt
# (s)plit, (y)es, (n)o, (q)uit

# Or interactively
git add -i
```

### 6. Submodules vs Subtrees

For including other repos:

| Submodules | Subtrees |
|------------|----------|
| Pointer to another repo | Copy of another repo |
| Separate .git | Part of your .git |
| Harder to use | Easier to use |
| Clone requires `--recursive` | Just works |

```bash
# Submodule
git submodule add https://github.com/user/lib.git

# Subtree
git subtree add --prefix=lib https://github.com/user/lib.git main --squash
```

### 7. Signed Commits

Prove commits came from you:

```bash
# Setup GPG key first, then:
git commit -S -m "Verified commit"

# Always sign
git config --global commit.gpgsign true

# Verify
git log --show-signature
```

### 8. Commit Message in HEREDOC

For multi-line messages:

```bash
git commit -m "$(cat <<'EOF'
Short summary line

Longer description here.
Can be multiple lines.

- Bullet points work
- Like this
EOF
)"
```

---

## Recovery & Safety

### Recover Deleted Branch

```bash
# Find the last commit of deleted branch
git reflog
# abc123 HEAD@{5}: checkout: moving from deleted-branch to main

# Recreate branch
git branch recovered-branch abc123
```

### Recover Deleted File

```bash
# Find when file was deleted
git log --all --full-history -- path/to/file.txt

# Restore it
git checkout abc123^ -- path/to/file.txt
```

### Recover Dropped Stash

```bash
# Find dangling commits
git fsck --no-reflog | grep commit

# Check each one
git show abc123

# Apply it
git stash apply abc123
```

### Undo Almost Anything

| Situation | Solution |
|-----------|----------|
| Undo last commit (keep changes) | `git reset --soft HEAD~1` |
| Undo last commit (discard) | `git reset --hard HEAD~1` |
| Undo a pushed commit | `git revert abc123` |
| Undo a merge | `git reset --hard ORIG_HEAD` |
| Undo a rebase | `git reset --hard ORIG_HEAD` |
| Undo git reset | `git reset --hard HEAD@{1}` |
| Undo file changes | `git checkout -- file.txt` |
| Undo staged file | `git reset HEAD file.txt` |

### The Nuclear Option

If everything is broken:

```bash
# Save your current changes
cp -r . ../backup

# Reset to remote state
git fetch origin
git reset --hard origin/main
```

---

## Performance & Optimization

### Shallow Clone

Clone only recent history:

```bash
git clone --depth 1 https://github.com/user/repo.git
# Only latest commit, no history

git clone --depth 10 https://github.com/user/repo.git
# Last 10 commits
```

### Sparse Checkout

Clone only specific folders:

```bash
git clone --filter=blob:none --sparse https://github.com/user/repo.git
cd repo
git sparse-checkout set folder1 folder2
```

### Large File Storage (LFS)

For large binary files:

```bash
# Install LFS
git lfs install

# Track large files
git lfs track "*.psd"
git lfs track "*.zip"

# Adds to .gitattributes
git add .gitattributes
```

### Garbage Collection

Clean up unnecessary files:

```bash
git gc                          # Basic cleanup
git gc --aggressive             # More thorough (slow)
git prune                       # Remove unreachable objects
```

### Repo Size Analysis

```bash
# Show repo size
du -sh .git

# Find large files in history
git rev-list --objects --all | \
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | \
  sed -n 's/^blob //p' | \
  sort -rnk2 | \
  head -20
```

---

## Quick Mental Models

### Commit = Snapshot + Metadata

```
Commit abc123
├── Tree (snapshot of all files)
├── Parent (previous commit)
├── Author
├── Date
└── Message
```

### Branch = Sticky Note on a Commit

```
main ──────▶ [commit C]
              │
feature ────▶ [commit E]
              │
              ├── [commit D]
              │
              └── [commit C] (common ancestor)
```

### HEAD = "You Are Here"

```
HEAD ──▶ main ──▶ [commit C]

After checkout feature:
HEAD ──▶ feature ──▶ [commit E]
```

---

*Remember: Git is designed to never lose data. If you made a commit, it exists somewhere. Use `git reflog` to find it.*
