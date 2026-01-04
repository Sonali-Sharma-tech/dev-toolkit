# Git Error Messages Decoded

When Git throws an error, find it here.

## Table of Contents
- [fatal: not a git repository](#fatal-not-a-git-repository)
- [You are in 'detached HEAD' state](#you-are-in-detached-head-state)
- [error: failed to push some refs](#error-failed-to-push-some-refs)
- [CONFLICT (content): Merge conflict](#conflict-content-merge-conflict)
- [fatal: refusing to merge unrelated histories](#fatal-refusing-to-merge-unrelated-histories)
- [error: Your local changes would be overwritten](#error-your-local-changes-would-be-overwritten)
- [fatal: The current branch has no upstream branch](#fatal-the-current-branch-has-no-upstream-branch)
- [error: pathspec did not match any files](#error-pathspec-did-not-match-any-files)
- [fatal: unable to access - Could not resolve host](#fatal-unable-to-access---could-not-resolve-host)
- [Permission denied (publickey)](#permission-denied-publickey)
- [error: insufficient permission for adding an object](#error-insufficient-permission-for-adding-an-object)
- [fatal: bad object HEAD](#fatal-bad-object-head)

---

## fatal: not a git repository

```
fatal: not a git repository (or any of the parent directories): .git
```

### What it means
You're running a git command in a folder that isn't a Git repository.

### Quick fix
```bash
# Option 1: Navigate to the correct directory
cd /path/to/your/repo

# Option 2: Initialize a new repository
git init
```

### If that doesn't work
```bash
# Check if .git folder exists
ls -la | grep .git

# You might be in a subfolder - go up
cd ..
git status
```

---

## You are in 'detached HEAD' state

```
You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.
```

### What it means
You checked out a specific commit instead of a branch. Any new commits won't belong to any branch.

### Quick fix
```bash
# Go back to a branch
git checkout main

# Or create a new branch from here
git checkout -b my-new-branch
```

### If you already made commits in detached HEAD
```bash
# Create a branch to save your work
git checkout -b save-my-work

# Now your commits are safe on this branch
```

---

## error: failed to push some refs

```
error: failed to push some refs to 'git@github.com:user/repo.git'
hint: Updates were rejected because the remote contains work that you do not
hint: have locally.
```

### What it means
Someone pushed changes to the remote branch after you last pulled.

### Quick fix
```bash
# Pull and rebase your changes on top
git pull --rebase origin main
git push
```

### If there are conflicts
```bash
# After pull --rebase, resolve conflicts then:
git add .
git rebase --continue
git push
```

### If you're sure your version is correct (dangerous)
```bash
# Force push - only if you're the only one on this branch!
git push --force-with-lease
```

---

## CONFLICT (content): Merge conflict

```
CONFLICT (content): Merge conflict in file.js
Automatic merge failed; fix conflicts and then commit the result.
```

### What it means
Two people edited the same lines. Git can't decide which version to keep.

### Quick fix
```bash
# 1. See which files have conflicts
git status

# 2. Open the file, look for conflict markers:
#    <<<<<<< HEAD
#    Your changes
#    =======
#    Their changes
#    >>>>>>> branch-name

# 3. Edit the file to keep what you want (remove the markers)

# 4. Mark as resolved
git add file.js

# 5. Complete the merge
git commit
```

### Use a merge tool
```bash
git mergetool
```

### Give up and start over
```bash
git merge --abort
```

---

## fatal: refusing to merge unrelated histories

```
fatal: refusing to merge unrelated histories
```

### What it means
You're trying to merge two repositories that don't share a common ancestor.

### Quick fix
```bash
# Allow unrelated histories to merge
git pull origin main --allow-unrelated-histories

# Or when merging
git merge other-branch --allow-unrelated-histories
```

### When this happens
- Merging a new remote into an existing local repo
- Reinitializing a repository
- Pulling from a different repository

---

## error: Your local changes would be overwritten

```
error: Your local changes to the following files would be overwritten by merge:
        file.js
Please commit your changes or stash them before you merge.
```

### What it means
You have uncommitted changes that conflict with incoming changes.

### Quick fix
```bash
# Option 1: Stash your changes temporarily
git stash
git pull
git stash pop

# Option 2: Commit your changes first
git add .
git commit -m "WIP: save my changes"
git pull
```

### If you want to discard your changes
```bash
# Discard all local changes (irreversible!)
git checkout -- .
git pull
```

---

## fatal: The current branch has no upstream branch

```
fatal: The current branch feature-xyz has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin feature-xyz
```

### What it means
Your local branch isn't linked to a remote branch yet.

### Quick fix
```bash
# Do exactly what Git suggests
git push --set-upstream origin feature-xyz

# Or shorter
git push -u origin feature-xyz

# For current branch (even shorter)
git push -u origin HEAD
```

---

## error: pathspec did not match any files

```
error: pathspec 'filename.js' did not match any file(s) known to git
```

### What it means
Git can't find the file you're trying to add/checkout/restore.

### Quick fix
```bash
# Check if file exists
ls filename.js

# Check if it's already tracked
git ls-files | grep filename

# If file exists but not tracked, add it
git add filename.js

# If trying to checkout, check the branch exists
git branch -a | grep branch-name
```

### Common causes
- Typo in filename
- File is in .gitignore
- File is in a different directory
- Branch name doesn't exist

---

## fatal: unable to access - Could not resolve host

```
fatal: unable to access 'https://github.com/user/repo.git/':
Could not resolve host: github.com
```

### What it means
DNS can't resolve the remote host. Network/internet issue.

### Quick fix
```bash
# Check internet connection
ping google.com

# Check if it's just GitHub
ping github.com

# Try using SSH instead of HTTPS
git remote set-url origin git@github.com:user/repo.git
```

### If you're behind a proxy
```bash
git config --global http.proxy http://proxy.example.com:8080
```

---

## Permission denied (publickey)

```
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.
```

### What it means
SSH authentication failed. Your key isn't set up or isn't being used.

### Quick fix
```bash
# Check if SSH agent has your key
ssh-add -l

# If empty, add your key
ssh-add ~/.ssh/id_ed25519

# Test SSH connection
ssh -T git@github.com
```

### If you don't have a key
```bash
# Generate new key
ssh-keygen -t ed25519 -C "your.email@example.com"

# Copy public key
cat ~/.ssh/id_ed25519.pub

# Add to GitHub: Settings → SSH Keys → New SSH Key
```

### If key exists but not working
```bash
# Check correct permissions
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
chmod 700 ~/.ssh

# Make sure key is added to GitHub
ssh -vT git@github.com  # Verbose mode for debugging
```

---

## error: insufficient permission for adding an object

```
error: insufficient permission for adding an object to repository database .git/objects
```

### What it means
File permission issue in the .git folder.

### Quick fix
```bash
# Fix ownership (replace with your user)
sudo chown -R $(whoami) .git

# Fix permissions
chmod -R 755 .git
```

### Why this happens
- Using `sudo git` commands (don't do this)
- Multiple users working on the same repo
- Copying repo from another location

---

## fatal: bad object HEAD

```
fatal: bad object HEAD
```

### What it means
Your repository is corrupted. The HEAD reference is broken.

### Quick fix
```bash
# Check what's wrong
git fsck --full

# If you know the last good commit
git update-ref HEAD abc123

# Nuclear option: re-clone
cd ..
rm -rf broken-repo
git clone <url>
```

### Recover from backup
```bash
# If you have reflog
git reflog

# Reset to a known good state
git reset --hard HEAD@{1}
```

---

## Quick Reference

| Error | Likely Cause | Quick Fix |
|-------|--------------|-----------|
| not a git repository | Wrong directory | `cd` to repo or `git init` |
| detached HEAD | Checked out a commit | `git checkout main` |
| failed to push | Remote has new commits | `git pull --rebase` |
| merge conflict | Same lines edited | Edit file, `git add`, `git commit` |
| unrelated histories | Different repos | `--allow-unrelated-histories` |
| local changes overwritten | Uncommitted work | `git stash` or commit first |
| no upstream branch | New branch | `git push -u origin HEAD` |
| pathspec not match | File doesn't exist | Check filename/path |
| could not resolve host | Network issue | Check internet/DNS |
| permission denied (publickey) | SSH key issue | `ssh-add` your key |
| insufficient permission | Wrong file ownership | `sudo chown -R $(whoami) .git` |

