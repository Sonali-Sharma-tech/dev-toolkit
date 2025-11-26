# Git Commands Reference

A comprehensive guide to useful git commands, from daily operations to advanced techniques.

## Table of Contents
- [Basic Commands](#basic-commands)
- [Branch Management](#branch-management)
- [Remote Operations](#remote-operations)
- [Merge Operations](#merge-operations)
- [Log and History](#log-and-history)
- [Staging and Unstaging](#staging-and-unstaging)
- [Tags](#tags)
- [Advanced Git Commands](#advanced-git-commands)

---

## Basic Commands

### Check status
```bash
git status
```
Shows the current state of your working directory and staging area.

### Show changes
```bash
git diff                    # Unstaged changes
git diff --staged           # Staged changes
git diff HEAD              # All changes (staged + unstaged)
```

### View commit log
```bash
git log --oneline          # Compact one-line format
git log --oneline --graph  # With branch graph
git log -p                 # Show patches (actual changes)
git log -n 5               # Last 5 commits
```

---

## Branch Management

### Create and switch to a new branch
```bash
git checkout -b new-feature
```
Creates a new branch and immediately switches to it.

### List all branches (local and remote)
```bash
git branch -a && git status
```
Shows all local and remote branches, then displays the current working tree status.

### List local branches only
```bash
git branch --list
```
Lists all local branches in the repository.

### List branches without pager or color
```bash
git --no-pager branch --no-color
```
Useful for scripting - outputs branches in plain text without pagination or color codes.

### Switch to a specific branch
```bash
git checkout dark-night
```
Switches to the branch named `dark-night`.

### Delete a local branch (force)
```bash
git branch -D dark-night
```
Force deletes the local branch even if it hasn't been merged.

### Delete a remote branch
```bash
git push origin --delete dark-night
```
Deletes the branch from the remote repository.

---

## Remote Operations

### List all remote branches
```bash
git ls-remote --heads origin
```
Shows all branches available on the remote repository without fetching them.

---

## Merge Operations

### Test merge without committing
```bash
git merge main --no-commit --no-ff 2>&1
```
Performs a merge but doesn't create a commit. Useful for testing if a merge will succeed. The `--no-ff` ensures a merge commit will be created (when you do commit). `2>&1` redirects stderr to stdout.

### Abort a merge
```bash
git merge --abort
```
Cancels a merge in progress and returns to the state before the merge started.

---

## Log and History

### Show commits between branches
```bash
git log --oneline --no-color main..dark-night
```
Shows all commits in `dark-night` that are not in `main`. One line per commit, no color output.

### Count co-authored commits
```bash
git log --format="%B" --no-color optimisation | grep -i "co-authored" | wc -l
```
Counts how many commits in the `optimisation` branch have "Co-authored-by" trailers.

### Rewrite commit messages (advanced)
```bash
git filter-branch --msg-filter
```
Rewrites commit messages in history. **Warning: This rewrites history, use with caution!**

---

## Staging and Unstaging

### Unstage a specific file
```bash
git reset HEAD path/to/file
git reset path/to/file          # Short form
```
Removes a file from staging area (keeps changes in working directory).

### Unstage all files
```bash
git reset HEAD
```

### Untrack files that are now in .gitignore
```bash
git rm -r --cached .
git add .
git commit -m "Remove ignored files from tracking"
```
Useful when you add files to `.gitignore` after they've already been committed.

### Untrack a specific file but keep it locally
```bash
git rm --cached path/to/file
```

---

## Tags

### List all tags
```bash
git tag
git tag -l "v1.*"              # List tags matching pattern
```

### Create an annotated tag
```bash
git tag -a v1.0.0 -m "Release version 1.0.0"
```
Annotated tags store metadata (author, date, message).

### Create a lightweight tag
```bash
git tag v1.0.0
```

### Tag a specific commit
```bash
git tag -a v1.0.0 <commit-hash> -m "Message"
```

### Push tags to remote
```bash
git push origin v1.0.0         # Push specific tag
git push origin --tags         # Push all tags
```

### Delete a local tag
```bash
git tag -d v1.0.0
```

### Delete a remote tag
```bash
git push origin --delete v1.0.0
git push origin :refs/tags/v1.0.0    # Alternative syntax
```

### Checkout a specific tag
```bash
git checkout v1.0.0
```

---

## Advanced Git Commands
*These commands are incredibly useful but not needed daily*

### 1. Interactive Rebase - Clean Up Commit History
```bash
git rebase -i HEAD~5
```
Interactively edit the last 5 commits. You can:
- Squash commits together
- Reword commit messages
- Reorder commits
- Drop commits entirely

### 2. Find When Code Was Added or Removed (Pickaxe)
```bash
git log -S "LoginViewController" --oneline
git log -S "function_name" --source --all
```
**SUPER USEFUL!** Finds commits that added or removed a specific string. Great for tracking when code was introduced or deleted.

### 3. Find Which Commit Introduced a Bug
```bash
git bisect start
git bisect bad                  # Current commit is bad
git bisect good <commit-hash>   # Known good commit
# Git will checkout commits for you to test
# Mark each as 'git bisect good' or 'git bisect bad'
git bisect reset               # When done
```
Binary search through history to find the commit that introduced a bug.

### 4. Stash with a Message
```bash
git stash push -m "WIP: feature implementation"
```
Save your work with a descriptive message for easier identification later.

### 5. Apply Specific Stash
```bash
git stash list                    # List all stashes
git stash apply stash@{2}         # Apply specific stash
git stash pop stash@{2}           # Apply and remove specific stash
```

### 6. Cherry-pick a Range of Commits
```bash
git cherry-pick A^..B
```
Apply commits from A (inclusive) to B from another branch.

### 7. Show What Changed in a Merge Commit
```bash
git show <merge-commit-hash>
```
See the actual changes introduced by a merge commit.

### 8. Find When a File Was Deleted
```bash
git log --all --full-history -- path/to/file
```
Shows the history of a file even if it was deleted.

### 9. Restore a Deleted File
```bash
git checkout <commit-hash>^ -- path/to/file
```
Restore a file from the commit before it was deleted.

### 10. Ignore Local Changes to a Tracked File
```bash
git update-index --assume-unchanged path/to/file
```
Tell git to ignore changes to a file (useful for local config files).
To undo: `git update-index --no-assume-unchanged path/to/file`

### 11. Show Files in a Commit
```bash
git show --name-only <commit-hash>
```
List all files that were changed in a specific commit.

### 12. Compare Branches
```bash
git diff branch1..branch2                    # Show differences
git log --oneline --graph branch1..branch2   # Show commits
git diff --name-status branch1..branch2      # Show changed files
```

### 13. Find Commits by Author
```bash
git log --author="John Doe" --oneline
```

### 14. Find Commits by Message Content
```bash
git log --grep="bug fix" --oneline
```

### 15. Show Commits That Modified a Specific Function
```bash
git log -L :functionName:path/to/file
```
Shows the history of changes to a specific function.

### 16. Undo Last Commit (Keep Changes)
```bash
git reset --soft HEAD~1
```
Undoes the last commit but keeps all changes staged.

### 17. Undo Last Commit (Discard Changes)
```bash
git reset --hard HEAD~1
```
**Warning: This permanently deletes changes!**

### 18. Create a Branch from a Specific Commit
```bash
git branch new-branch <commit-hash>
```

### 19. See Who Changed Each Line of a File
```bash
git blame path/to/file
git blame -L 10,20 path/to/file  # Specific lines
```

### 20. Create an Orphan Branch (No History)
```bash
git checkout --orphan new-branch
```
Useful for GitHub Pages or starting fresh.

### 21. Prune Deleted Remote Branches Locally
```bash
git fetch --prune
git remote prune origin
```
Removes local references to branches that were deleted on remote.

### 22. Show All Branches Containing a Commit
```bash
git branch --contains <commit-hash>
```

### 23. Rewrite Author for Last Commit
```bash
git commit --amend --author="Name <email@example.com>" --no-edit
```

### 24. Find Large Files in Repository
```bash
git rev-list --objects --all | \
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | \
  sed -n 's/^blob //p' | \
  sort --numeric-sort --key=2 | \
  tail -n 10
```

### 25. Clean Untracked Files
```bash
git clean -fd              # Remove untracked files and directories
git clean -fdn             # Dry run - show what would be deleted
git clean -fdx             # Also remove ignored files
```

### 26. Create a Patch File
```bash
git format-patch -1 <commit-hash>           # Single commit
git format-patch main..feature-branch       # All commits in range
```

### 27. Apply a Patch
```bash
git apply patch-file.patch
```

### 28. Show Configuration
```bash
git config --list                           # All config
git config --list --show-origin             # With file locations
```

### 29. Temporarily Ignore File Mode Changes
```bash
git config core.fileMode false
```

### 30. Find Common Ancestor of Two Branches
```bash
git merge-base branch1 branch2
```

### 31. View Reflog (Command History)
```bash
git reflog
```
Shows a log of all git commands that modified HEAD. Useful for recovering lost commits!

### 32. Revert a Commit (Safe Undo)
```bash
git revert HEAD                # Revert last commit
git revert <commit-hash>       # Revert specific commit
git revert HEAD~3              # Revert 4th last commit
```
Creates a new commit that undoes changes. **Safer than reset** because it preserves history - ideal for shared branches.

### 33. Amend the Last Commit
```bash
git commit --amend                              # Edit commit message
git commit --amend --no-edit                    # Keep same message, add staged changes
git commit --amend -m "New commit message"      # Change message directly
```
Modifies the most recent commit. Useful for fixing typos or adding forgotten changes.

### 34. Basic Rebase
```bash
git rebase main                # Rebase current branch onto main
git rebase --continue          # Continue after resolving conflicts
git rebase --abort             # Cancel rebase
git rebase --skip              # Skip current commit
```
Replays commits from current branch on top of another branch. Makes history linear.

### 35. Rebase with Branch
```bash
git rebase main feature-branch
```
Rebases `feature-branch` onto `main` without checking out the branch.

### 36. Show File at Specific Commit
```bash
git show <commit-hash>:path/to/file
```
Display contents of a file as it existed in a specific commit.

---

## Quick Tips

- **Undo almost anything**: Check `git reflog` first - it's a lifesaver!
- **Before force push**: Always create a backup branch
- **Testing destructive commands**: Use `--dry-run` flag when available
- **Scripting**: Use `--no-pager` and `--no-color` flags for clean output

---

*Last updated: 2025-11-23*
