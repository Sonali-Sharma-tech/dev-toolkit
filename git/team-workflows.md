# Git Team Workflows

Collaborate effectively with your team using these patterns and commands.

## Table of Contents
- [Pull Requests](#pull-requests)
- [Merge Conflicts](#merge-conflicts)
- [Cherry-pick](#cherry-pick)
- [Stash](#stash)
- [Working with Remotes](#working-with-remotes)
- [Code Review Commands](#code-review-commands)
- [Common Team Patterns](#common-team-patterns)

---

## Pull Requests

### Create a PR-Ready Branch
```bash
# Create feature branch from updated main
git checkout main
git pull origin main
git checkout -b feature/add-login

# Work on your feature...
git add .
git commit -m "Add login functionality"

# Push and set upstream
git push -u origin feature/add-login
```

### Create PR with GitHub CLI
```bash
# Install gh if needed: brew install gh

# Create PR interactively
gh pr create

# Create PR with title and body
gh pr create --title "Add login feature" --body "Implements user authentication"

# Create draft PR
gh pr create --draft

# Create PR with reviewers
gh pr create --reviewer teammate1,teammate2

# Create PR targeting specific branch
gh pr create --base develop
```

### Review PRs
```bash
# List open PRs
gh pr list

# View specific PR
gh pr view 123

# View PR in browser
gh pr view 123 --web

# Check out PR locally
gh pr checkout 123

# Review PR
gh pr review 123 --approve
gh pr review 123 --request-changes --body "Please fix..."
gh pr review 123 --comment --body "Looks good overall"
```

### Merge PRs
```bash
# Merge PR
gh pr merge 123

# Merge with squash
gh pr merge 123 --squash

# Merge with rebase
gh pr merge 123 --rebase

# Delete branch after merge
gh pr merge 123 --delete-branch
```

### Update PR Branch
```bash
# Fetch latest main and rebase
git fetch origin main
git rebase origin/main

# Or merge main into your branch
git merge origin/main

# Push updated branch
git push --force-with-lease
```

---

## Merge Conflicts

### Understanding Conflicts
```
<<<<<<< HEAD
Your changes (current branch)
=======
Their changes (incoming branch)
>>>>>>> feature-branch
```

### Resolve Conflicts Manually
```bash
# See which files have conflicts
git status

# Open conflicted file, edit to keep desired code
# Remove conflict markers: <<<<<<<, =======, >>>>>>>

# After fixing, stage the file
git add path/to/file.js

# Continue the merge
git commit

# Or if rebasing
git rebase --continue
```

### Conflict Resolution Tools
```bash
# Use merge tool
git mergetool

# Configure VS Code as merge tool
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'

# See what changed on each side
git diff --ours    # Your version
git diff --theirs  # Their version
git diff --base    # Common ancestor
```

### Accept One Side Entirely
```bash
# Keep your version (current branch)
git checkout --ours path/to/file.js
git add path/to/file.js

# Keep their version (incoming branch)
git checkout --theirs path/to/file.js
git add path/to/file.js
```

### Abort if Overwhelmed
```bash
# Abort merge
git merge --abort

# Abort rebase
git rebase --abort

# Abort cherry-pick
git cherry-pick --abort
```

### Prevent Conflicts
```bash
# Regularly update your branch
git fetch origin main
git rebase origin/main

# Before starting work
git pull --rebase origin main

# See what will conflict before merging
git merge --no-commit --no-ff feature-branch
git diff --cached  # See incoming changes
git merge --abort  # Cancel if looks problematic
```

---

## Cherry-pick

Apply specific commits to your current branch.

### Basic Cherry-pick
```bash
# Apply single commit
git cherry-pick abc123

# Apply multiple commits
git cherry-pick abc123 def456 ghi789

# Apply range of commits (exclusive of first)
git cherry-pick abc123..ghi789

# Apply range (inclusive)
git cherry-pick abc123^..ghi789
```

### Cherry-pick Options
```bash
# Don't commit immediately (stage only)
git cherry-pick --no-commit abc123

# Edit commit message
git cherry-pick --edit abc123

# Add signoff
git cherry-pick --signoff abc123

# Keep original commit info in message
git cherry-pick -x abc123
```

### Handle Cherry-pick Conflicts
```bash
# If conflict occurs during cherry-pick
git status  # See conflicts

# Fix conflicts, then
git add .
git cherry-pick --continue

# Or abort
git cherry-pick --abort

# Skip this commit, continue with next
git cherry-pick --skip
```

### Common Cherry-pick Scenarios
```bash
# Backport fix to release branch
git checkout release-1.0
git cherry-pick abc123  # Hotfix from main

# Pick feature from another branch
git checkout main
git log feature-branch --oneline  # Find commit
git cherry-pick def456

# Pick last commit from another branch
git cherry-pick other-branch
```

---

## Stash

Temporarily save uncommitted changes.

### Basic Stash Operations
```bash
# Stash current changes
git stash

# Stash with message
git stash push -m "Work in progress on login"

# List all stashes
git stash list

# Apply latest stash (keep in stash list)
git stash apply

# Apply and remove from stash list
git stash pop

# Apply specific stash
git stash apply stash@{2}
git stash pop stash@{1}
```

### Stash Options
```bash
# Include untracked files
git stash -u
git stash --include-untracked

# Include ignored files too
git stash -a
git stash --all

# Stash only staged changes
git stash --staged

# Stash specific files
git stash push -m "Config changes" path/to/config.js

# Interactive stash (select hunks)
git stash push -p
```

### View Stash Contents
```bash
# Show stash diff
git stash show

# Show with full diff
git stash show -p

# Show specific stash
git stash show -p stash@{2}

# List files in stash
git stash show --name-only stash@{0}
```

### Manage Stashes
```bash
# Drop specific stash
git stash drop stash@{2}

# Drop latest stash
git stash drop

# Clear all stashes (⚠️ irreversible)
git stash clear

# Create branch from stash
git stash branch new-branch stash@{0}
```

### Stash Workflow Example
```bash
# Working on feature, need to fix urgent bug
git stash push -m "Feature work in progress"

# Switch to main, fix bug
git checkout main
git checkout -b hotfix
# ... fix bug ...
git commit -am "Fix critical bug"
git checkout main
git merge hotfix

# Return to feature work
git checkout feature-branch
git stash pop
```

---

## Working with Remotes

### Multiple Remotes
```bash
# List remotes
git remote -v

# Add remote
git remote add upstream https://github.com/original/repo.git

# Rename remote
git remote rename origin github

# Remove remote
git remote remove upstream

# Change remote URL
git remote set-url origin git@github.com:user/repo.git
```

### Sync with Upstream (Forks)
```bash
# Add upstream remote
git remote add upstream https://github.com/original/repo.git

# Fetch upstream changes
git fetch upstream

# Merge upstream into local main
git checkout main
git merge upstream/main

# Push to your fork
git push origin main
```

### Fetch vs Pull
```bash
# Fetch: Download changes, don't merge
git fetch origin

# Fetch all remotes
git fetch --all

# Pull: Fetch + merge
git pull origin main

# Pull with rebase (cleaner history)
git pull --rebase origin main

# Set pull to rebase by default
git config --global pull.rebase true
```

### Push Options
```bash
# Push to remote
git push origin main

# Push and set upstream
git push -u origin feature-branch

# Push all branches
git push --all origin

# Push tags
git push --tags

# Force push (⚠️ be careful)
git push --force

# Safer force push (fails if remote has new commits)
git push --force-with-lease
```

---

## Code Review Commands

### See What Changed in a PR
```bash
# Fetch PR locally
gh pr checkout 123

# See all changes vs main
git diff main...HEAD

# See changed files
git diff --name-only main...HEAD

# See commit history
git log main..HEAD --oneline
```

### Review Changes by File
```bash
# Diff specific file
git diff main...HEAD -- src/app.js

# Blame to see who wrote what
git blame src/app.js

# History of specific file
git log --oneline -- src/app.js
```

### Test PR Locally
```bash
# Checkout PR
gh pr checkout 123

# Run tests
npm test

# Build
npm run build

# Return to your branch
git checkout -
```

### Suggest Changes
```bash
# Create fixup commit
git commit --fixup abc123

# Author can then squash
git rebase -i --autosquash main
```

---

## Common Team Patterns

### Feature Branch Workflow
```bash
# Start feature
git checkout main
git pull
git checkout -b feature/user-auth

# Work...
git add .
git commit -m "Add auth middleware"

# Keep updated with main
git fetch origin main
git rebase origin/main

# Push for PR
git push -u origin feature/user-auth

# After PR merged, cleanup
git checkout main
git pull
git branch -d feature/user-auth
```

### Gitflow-style Release
```bash
# Create release branch
git checkout develop
git checkout -b release/1.2.0

# Fix bugs on release branch...
git commit -m "Fix last minute bug"

# Merge to main
git checkout main
git merge --no-ff release/1.2.0
git tag -a v1.2.0 -m "Release 1.2.0"

# Merge back to develop
git checkout develop
git merge --no-ff release/1.2.0

# Delete release branch
git branch -d release/1.2.0
```

### Hotfix Workflow
```bash
# Create hotfix from main
git checkout main
git checkout -b hotfix/critical-bug

# Fix the bug
git commit -m "Fix critical auth bypass"

# Merge to main
git checkout main
git merge --no-ff hotfix/critical-bug
git tag -a v1.2.1 -m "Hotfix 1.2.1"
git push origin main --tags

# Merge to develop too
git checkout develop
git merge --no-ff hotfix/critical-bug
git push origin develop

# Delete hotfix branch
git branch -d hotfix/critical-bug
```

### Squash Before Merge
```bash
# On your feature branch, squash commits
git rebase -i main

# Change 'pick' to 'squash' for commits to combine
# Edit the combined commit message
# Force push
git push --force-with-lease
```

---

## Quick Reference

| Task | Command |
|------|---------|
| Create PR | `gh pr create` |
| List open PRs | `gh pr list` |
| Checkout PR | `gh pr checkout 123` |
| Save work temporarily | `git stash push -m "message"` |
| Restore stashed work | `git stash pop` |
| Apply specific commit | `git cherry-pick abc123` |
| Keep our version in conflict | `git checkout --ours file` |
| Keep their version | `git checkout --theirs file` |
| Abort merge | `git merge --abort` |
| Sync fork with upstream | `git fetch upstream && git merge upstream/main` |
| Safe force push | `git push --force-with-lease` |

