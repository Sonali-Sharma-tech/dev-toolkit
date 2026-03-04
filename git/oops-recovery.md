# Git Oops Recovery Guide

When things go wrong in Git, don't panic. This guide has your back.

## Table of Contents
- [I committed to the wrong branch](#i-committed-to-the-wrong-branch)
- [I need to undo my last commit](#i-need-to-undo-my-last-commit)
- [I pushed secrets/credentials](#i-pushed-secretscredentials)
- [I need to undo multiple commits](#i-need-to-undo-multiple-commits)
- [My rebase went wrong](#my-rebase-went-wrong)
- [I accidentally deleted a branch](#i-accidentally-deleted-a-branch)
- [I committed to main/master by mistake](#i-committed-to-mainmaster-by-mistake)
- [I need to remove a file from the last commit](#i-need-to-remove-a-file-from-the-last-commit)
- [I messed up a merge](#i-messed-up-a-merge)
- [I lost commits and need them back](#i-lost-commits-and-need-them-back)
- [I want to undo a pushed commit](#i-want-to-undo-a-pushed-commit)
- [I accidentally staged files I shouldn't have](#i-accidentally-staged-files-i-shouldnt-have)
- [I want to remove a file from a PR](#i-want-to-remove-a-file-from-a-pr)
- [Nuclear Options](#nuclear-options)

---

## I committed to the wrong branch

### Scenario: Committed to main but meant to commit to feature-branch

**Option 1: Move commit to correct branch (not pushed yet)**
```bash
# Create new branch from current state (keeps the commit)
git branch feature-branch

# Reset main to before the commit
git reset --hard HEAD~1

# Switch to feature branch
git checkout feature-branch
```

**Option 2: Cherry-pick to correct branch (already pushed)**
```bash
# Note the commit hash
git log --oneline -1
# abc123

# Switch to correct branch
git checkout feature-branch

# Cherry-pick the commit
git cherry-pick abc123

# Go back to main and revert
git checkout main
git revert abc123
git push
```

> **Tip**: If you haven't pushed, `reset --hard` is cleaner. If pushed, use `revert` to maintain history.

---

## I need to undo my last commit

### Keep changes, uncommit only
```bash
# Undo commit, keep changes staged
git reset --soft HEAD~1

# Undo commit, keep changes unstaged
git reset HEAD~1

# Same as above (default is --mixed)
git reset --mixed HEAD~1
```

### Discard changes completely
```bash
# ⚠️ DESTRUCTIVE: Removes commit AND changes
git reset --hard HEAD~1
```

### Visual Guide
```
HEAD~1 means "one commit before HEAD"

Before: A -- B -- C (HEAD)
After:  A -- B (HEAD)    C is gone (or kept based on flag)

--soft:  Changes stay staged
--mixed: Changes stay unstaged (default)
--hard:  Changes deleted
```

---

## I pushed secrets/credentials

### 🚨 IMPORTANT: Rotate credentials FIRST
**Before doing anything in git, go rotate/revoke the exposed credentials!**

### Remove from history (small repos)
```bash
# Remove file from entire history
git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch path/to/secret-file' \
  --prune-empty --tag-name-filter cat -- --all

# Force push all branches
git push origin --force --all
git push origin --force --tags
```

### Using BFG Repo Cleaner (faster for large repos)
```bash
# Install BFG
brew install bfg

# Remove file from history
bfg --delete-files secret-file.txt

# Or replace text in files
bfg --replace-text passwords.txt

# Clean up and push
git reflog expire --expire=now --all
git gc --prune=now --aggressive
git push --force
```

### Quick fix if just pushed
```bash
# If caught immediately (within seconds)
git reset --hard HEAD~1
git push --force

# Then rotate your credentials anyway!
```

> **Warning**: Anyone who pulled between your push and force push still has the secrets. Always rotate credentials.

---

## I need to undo multiple commits

### Keep all changes, uncommit N commits
```bash
# Undo last 3 commits, keep changes staged
git reset --soft HEAD~3

# Undo last 3 commits, keep changes unstaged
git reset HEAD~3
```

### Discard multiple commits completely
```bash
# ⚠️ DESTRUCTIVE: Removes last 3 commits AND changes
git reset --hard HEAD~3
```

### Reset to specific commit
```bash
# Find the commit you want to go back to
git log --oneline

# Reset to that commit (keeps changes)
git reset abc123

# Reset to that commit (discards changes)
git reset --hard abc123
```

---

## My rebase went wrong

### Abort rebase in progress
```bash
# Stop the rebase, go back to before you started
git rebase --abort
```

### Already completed bad rebase (not pushed)
```bash
# Find the commit before rebase started
git reflog

# Look for entry like: "rebase started"
# Find the commit hash before that

# Reset to that point
git reset --hard abc123
```

### Already pushed bad rebase
```bash
# If others haven't pulled, force push the fix
git reset --hard abc123
git push --force

# If others have pulled, coordinate with team
# May need to have everyone reset their local branches
```

> **Tip**: `git reflog` is your time machine. It shows everywhere HEAD has been.

---

## I accidentally deleted a branch

### Just deleted locally
```bash
# Find the commit the branch pointed to
git reflog

# Look for: "checkout: moving from branch-name to..."
# Note the commit hash

# Recreate the branch
git branch branch-name abc123
```

### Deleted from remote
```bash
# If you still have it locally
git push origin branch-name

# If deleted everywhere, check reflog
git reflog
git branch branch-name abc123
git push origin branch-name
```

### Find deleted branch commits
```bash
# Show all recent branch tips
git fsck --lost-found

# Or search reflog for branch name
git reflog | grep "branch-name"
```

---

## I committed to main/master by mistake

### Haven't pushed yet
```bash
# Create the branch you meant to use
git branch correct-branch

# Reset main to remote state
git reset --hard origin/main

# Switch to your branch
git checkout correct-branch
```

### Already pushed to protected branch
```bash
# Create branch with your changes
git branch my-changes

# Revert on main (creates new commit undoing changes)
git revert HEAD
git push

# Switch to branch and continue work
git checkout my-changes
```

---

## I need to remove a file from the last commit

### Remove file but keep it in working directory
```bash
# Unstage and remove from commit
git reset HEAD~1 --soft
git reset HEAD path/to/file
git commit -c ORIG_HEAD
```

### Simpler: Amend the commit
```bash
# Remove file from staging
git rm --cached path/to/file

# Add to .gitignore if needed
echo "path/to/file" >> .gitignore
git add .gitignore

# Amend the commit
git commit --amend
```

### Already pushed
```bash
# Do the above, then force push
git push --force
```

---

## I messed up a merge

### Abort merge in progress
```bash
# Conflicts everywhere? Just stop
git merge --abort
```

### Undo completed merge (not pushed)
```bash
# Go back to before the merge
git reset --hard HEAD~1

# Or find the exact commit
git reflog
git reset --hard abc123
```

### Undo pushed merge
```bash
# Revert the merge commit (keeps history)
# -m 1 specifies which parent to revert to (usually 1)
git revert -m 1 HEAD
git push
```

---

## I lost commits and need them back

### The reflog saves everything
```bash
# See everywhere HEAD has been
git reflog

# Shows output like:
# abc123 HEAD@{0}: commit: Latest commit
# def456 HEAD@{1}: reset: moving to HEAD~3
# ghi789 HEAD@{2}: commit: The commit I lost

# Recover by resetting
git reset --hard ghi789

# Or create a branch from it
git branch recovered-commits ghi789
```

### Find dangling commits
```bash
# Find commits not attached to any branch
git fsck --lost-found

# Check each dangling commit
git show abc123

# Recover by creating branch
git branch recovered abc123
```

> **Note**: Reflog entries expire after 90 days by default. Act within that window.

---

## I want to undo a pushed commit

### Best practice: Revert (preserves history)
```bash
# Create a new commit that undoes the changes
git revert abc123
git push
```

### Revert multiple commits
```bash
# Revert a range (oldest..newest)
git revert abc123..def456

# Or revert specific commits
git revert abc123 def456 ghi789
```

### Force push (rewrites history)
```bash
# ⚠️ Only if you're the only one using the branch
git reset --hard HEAD~1
git push --force

# Safer force push (fails if remote has new commits)
git push --force-with-lease
```

---

## I accidentally staged files I shouldn't have

### Unstage specific file
```bash
git reset HEAD path/to/file

# Or newer syntax
git restore --staged path/to/file
```

### Unstage all files
```bash
git reset HEAD

# Or
git restore --staged .
```

### Prevent accidental staging
```bash
# Add to .gitignore
echo "*.log" >> .gitignore
echo "node_modules/" >> .gitignore
echo ".env" >> .gitignore
```

---

## Nuclear Options

### Start fresh but keep current code
```bash
# Save your current work
cp -r . ../backup

# Reset everything to remote
git fetch origin
git reset --hard origin/main
```

### Complete history reset
```bash
# ⚠️ LAST RESORT: Deletes all history
rm -rf .git
git init
git add .
git commit -m "Fresh start"
git remote add origin <url>
git push --force
```

### Clone fresh and copy changes
```bash
# Clone a fresh copy
git clone <url> fresh-copy

# Copy your changed files over
cp -r your-changes/* fresh-copy/

# Commit in fresh copy
cd fresh-copy
git add .
git commit -m "Apply changes"
```

---

## I want to remove a file from a PR

### Step 1: Check if the file exists on main
```bash
git show main:path/to/file
# If "fatal: path exists on disk, but not in 'main'" → file is NOT on main
# If it prints file contents → file EXISTS on main
```

### File NOT on main (added only in your branch)
```bash
# Remove from git tracking (keeps file on disk)
git rm --cached path/to/file

# Commit and push
git commit -m "remove file from tracking"
git push
```
Result: File disappears from PR entirely (never existed on main, doesn't exist in final state).

### File EXISTS on main (you modified it but don't want to)
```bash
# Restore to exact main version
git checkout main -- path/to/file

# Commit and push
git commit -m "restore file to main version"
git push
```
Result: File is identical to main, so PR shows no diff for it.

> **Key insight**: PRs compare your branch's final state against main. Make the file match main's state and it vanishes from the diff.

---

## Quick Reference Card

| Situation | Command |
|-----------|---------|
| Undo last commit (keep changes) | `git reset --soft HEAD~1` |
| Undo last commit (discard) | `git reset --hard HEAD~1` |
| Undo pushed commit | `git revert HEAD` |
| Abort merge | `git merge --abort` |
| Abort rebase | `git rebase --abort` |
| Find lost commits | `git reflog` |
| Unstage file | `git reset HEAD file` |
| Remove file from commit | `git rm --cached file` |
| Recover deleted branch | `git branch name $(git reflog | grep name | head -1 | cut -d' ' -f1)` |

---

## Prevention Tips

### Before committing
```bash
# Always check what you're committing
git status
git diff --staged
```

### Use hooks
```bash
# Pre-commit hook to prevent secrets
# .git/hooks/pre-commit
grep -r "password\|secret\|api_key" --include="*.js" && exit 1
```

### Protect branches
```bash
# On GitHub/GitLab, enable branch protection for main
# Requires PR reviews before merge
# Prevents force push
```

