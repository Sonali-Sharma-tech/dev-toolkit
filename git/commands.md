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
- [Git Worktrees](#git-worktrees)
- [GitHub CLI (gh) Commands](#github-cli-gh-commands)
- [Clone with Specific User Config](#clone-with-specific-user-config)

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

### Save diff to a file (quick patch)
```bash
git diff > ~/my-changes.diff                    # Save unstaged changes
git diff --staged > ~/staged-changes.diff       # Save staged changes
git diff HEAD > ~/all-changes.diff              # Save all changes
git diff main..feature > ~/branch-diff.diff    # Save diff between branches
```
Unlike `git stash`, this just saves the changes to a file without modifying your working directory. Useful for backup, sharing, or reviewing later. Apply with `git apply ~/my-changes.diff`.

### View commit log
```bash
git log                    # Full log (press q to exit)
git log --oneline          # Compact one-line format
git log --oneline --graph  # With branch graph
git log -p                 # Show patches (actual changes)
```

### View last N commits
```bash
git log -1                 # Last commit only
git log -5                 # Last 5 commits
git log -n 10              # Last 10 commits (same as -10)
git log --oneline -5       # Last 5 in compact format
```

### View latest commit details
```bash
git log -1                           # Last commit (full details)
git log -1 --stat                    # Last commit with file changes
git log -1 --name-only               # Last commit with file names
git log -1 --format="%H"             # Just the commit hash
git log -1 --format="%h %s"          # Short hash + message
git log -1 --format="%an - %s"       # Author name + message
```

### View specific commit (git show)
```bash
git show                             # Last commit with diff
git show --stat                      # Last commit with stats
git show abc1234                     # Specific commit by hash
git show abc1234 --stat              # Specific commit with file stats
git show abc1234 --name-only         # Just file names changed
git show abc1234 --no-stat -p        # Only the diff (no file stats)
git show abc1234:path/to/file.txt    # File content at that commit
git show HEAD~2                      # 2 commits before HEAD
git show main:README.md              # File content on main branch
```
`--no-stat` hides the file change summary, `-p` shows the patch (diff).

### Custom log formats
```bash
git log --format="%h %an %ar %s"     # hash, author, time ago, message
git log --format="%h %ad %s" --date=short  # hash, date, message
git log --pretty=format:"%C(yellow)%h%C(reset) %s %C(blue)<%an>%C(reset)"  # Colored
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

### Force Push (Safe vs Dangerous)
```bash
# DANGEROUS - overwrites remote history blindly
git push --force                    # Avoid this!
git push -f                         # Same thing, still dangerous

# SAFE - only pushes if remote hasn't changed
git push --force-with-lease         # Recommended!
git push --force-with-lease origin feature-branch
```
`--force-with-lease` is a safer alternative to `--force`. It checks that the remote branch hasn't been updated by someone else since your last fetch. If it has, the push is rejected — preventing you from accidentally overwriting a teammate's commits.

**When to use force push:**
- After rebasing a feature branch
- After amending commits
- After squashing commits
- Cleaning up PR history before merge

**Always use `--force-with-lease` instead of `--force`** unless you're absolutely certain you want to overwrite remote changes.

```bash
# Set as alias for safety
git config --global alias.pushf "push --force-with-lease"
# Now use: git pushf
```

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

### 37. Pull with Rebase (Linear History)
```bash
git pull --rebase origin main
# Equivalent to:
# git fetch origin main
# git rebase origin/main
```
Instead of creating a merge commit, replays your local commits on top of remote changes. Keeps history linear and clean.

**When to use:**
- Before pushing your changes
- On feature branches
- When you want clean history

**Don't use when:**
- Commits are already pushed and shared
- Working on main/master branch with others

Configure as default:
```bash
git config pull.rebase true              # For current repo
git config --global pull.rebase true     # For all repos
```

---

## Git Worktrees
*Work on multiple branches simultaneously without stashing or switching*

Worktrees let you checkout multiple branches at once, each in its own directory. Great for reviewing PRs while working on a feature, or running tests on one branch while coding on another.

### List worktrees
```bash
# Show all worktrees (main repo + any linked worktrees)
git worktree list
```

### Create a new worktree
```bash
# Create worktree for existing branch in sibling directory
# Creates ../feature-branch directory with that branch checked out
git worktree add ../feature-branch feature-branch

# Create worktree with new branch (branching from current HEAD)
git worktree add -b new-feature ../new-feature

# Create worktree for new branch based on specific commit/branch
git worktree add -b hotfix ../hotfix main

# Create worktree in custom path
git worktree add /path/to/worktree branch-name
```

### Worktree for reviewing PRs
```bash
# Fetch PR and create worktree to review it (keeps your work untouched)
git fetch origin pull/123/head:pr-123
git worktree add ../pr-123-review pr-123

# Review the PR in ../pr-123-review, your main work stays in current directory
```

### Remove a worktree
```bash
# Remove worktree (after you're done with it)
git worktree remove ../feature-branch

# Force remove if worktree has changes
git worktree remove --force ../feature-branch

# Or just delete the directory and prune
rm -rf ../feature-branch
git worktree prune       # Clean up stale worktree references
```

### Worktree with detached HEAD
```bash
# Checkout specific commit without creating branch (detached HEAD)
git worktree add --detach ../test-commit abc1234

# Useful for testing old versions or specific commits
```

### Move a worktree
```bash
# Relocate worktree to different path
git worktree move ../old-path ../new-path
```

### Lock/unlock worktree
```bash
# Prevent worktree from being pruned (useful on removable drives)
git worktree lock ../feature-branch
git worktree lock --reason "On USB drive" ../feature-branch

# Unlock when done
git worktree unlock ../feature-branch
```

### Common worktree workflow
```bash
# You're working on feature-a, but need to review a PR urgently
git worktree add ../pr-review origin/pr-branch

# Now you have:
# /projects/repo          <- your feature-a work (untouched)
# /projects/pr-review     <- PR code to review

cd ../pr-review
# ... review, test, comment on PR ...

# When done, clean up
cd ../repo
git worktree remove ../pr-review
```

### Worktree vs Stash vs Clone
| Method | Use When | Pros | Cons |
|--------|----------|------|------|
| Worktree | Frequent branch switching, PR reviews | Fast, shares .git, no stash needed | Extra directories |
| Stash | Quick temporary switch | Simple, no extra dirs | Can lose stashes, merge conflicts |
| Clone | Completely isolated work | Full isolation | Slow, uses more disk, separate remotes |

---

## GitHub CLI (gh) Commands

### Authentication
```bash
gh auth status                      # View current auth status
gh auth login                       # Login (interactive)
gh auth login --with-token < token.txt  # Login with token
gh auth switch                      # Switch between accounts
gh auth switch -u username          # Switch to specific account
gh auth list                        # List all authenticated accounts
gh auth logout                      # Logout current account
gh auth logout -u username          # Logout specific account
gh auth refresh                     # Refresh stored credentials
```

### Pull Requests
```bash
# Create PRs
gh pr create                        # Interactive PR creation
gh pr create --title "Title" --body "Description"
gh pr create --draft                # Create as draft PR
gh pr create --base main            # Specify base branch
gh pr create --web                  # Open browser to create PR

# List and view PRs
gh pr list                          # List open PRs
gh pr list --state all              # List all PRs (open, closed, merged)
gh pr list --author @me             # Your PRs only
gh pr list --assignee username      # PRs assigned to user
gh pr list --label "bug"            # PRs with specific label
gh pr view 123                      # View PR #123 details
gh pr view 123 --web                # Open PR in browser
gh pr view --comments               # View PR with comments

# Work with PRs
gh pr checkout 123                  # Checkout PR branch locally
gh pr diff 123                      # View PR diff
gh pr merge 123                     # Merge PR (interactive)
gh pr merge 123 --squash            # Squash and merge
gh pr merge 123 --rebase            # Rebase and merge
gh pr merge 123 --merge             # Create merge commit
gh pr merge 123 --auto              # Auto-merge when checks pass
gh pr close 123                     # Close PR without merging
gh pr reopen 123                    # Reopen closed PR
gh pr ready 123                     # Mark draft as ready for review

# Review PRs
gh pr review 123 --approve          # Approve PR
gh pr review 123 --comment -b "LGTM"  # Comment on PR
gh pr review 123 --request-changes -b "Fix typo"

# PR checks
gh pr checks 123                    # View CI/CD status
gh pr checks 123 --watch            # Watch checks in real-time
```

### Issues
```bash
# Create issues
gh issue create                     # Interactive issue creation
gh issue create --title "Bug" --body "Description"
gh issue create --label "bug,urgent"
gh issue create --assignee @me
gh issue create --web               # Open browser to create

# List and view issues
gh issue list                       # List open issues
gh issue list --state all           # All issues
gh issue list --assignee @me        # Assigned to you
gh issue list --author @me          # Created by you
gh issue list --label "bug"         # Filter by label
gh issue list --search "keyword"    # Search issues
gh issue view 45                    # View issue #45
gh issue view 45 --web              # Open in browser
gh issue view 45 --comments         # View with comments

# Manage issues
gh issue close 45                   # Close issue
gh issue reopen 45                  # Reopen issue
gh issue edit 45 --title "New title"
gh issue edit 45 --add-label "priority"
gh issue edit 45 --add-assignee username
gh issue comment 45 --body "Comment text"
gh issue pin 45                     # Pin issue
gh issue unpin 45                   # Unpin issue
gh issue transfer 45 owner/new-repo # Transfer to another repo
```

### Repository
```bash
# Clone and create
gh repo clone owner/repo            # Clone repository
gh repo clone owner/repo -- --depth 1  # Shallow clone
gh repo create my-repo              # Create new repo (interactive)
gh repo create my-repo --public     # Create public repo
gh repo create my-repo --private    # Create private repo
gh repo create --source . --push    # Create from current directory
gh repo fork owner/repo             # Fork a repository
gh repo fork owner/repo --clone     # Fork and clone

# View and browse
gh repo view                        # View current repo info
gh repo view owner/repo             # View specific repo
gh repo view --web                  # Open repo in browser
gh repo list                        # List your repos
gh repo list owner                  # List user's repos
gh repo list --source               # Only non-forks
gh repo list --limit 50             # Limit results

# Manage repo
gh repo edit --default-branch main  # Change default branch
gh repo edit --visibility public    # Change visibility
gh repo rename new-name             # Rename repository
gh repo delete owner/repo --yes     # Delete repo (careful!)
gh repo sync                        # Sync fork with upstream
gh repo archive owner/repo          # Archive repository
```

### Workflow Runs (GitHub Actions)
```bash
# List and view runs
gh run list                         # List recent workflow runs
gh run list --workflow build.yml    # Filter by workflow
gh run list --branch main           # Filter by branch
gh run list --status failure        # Filter by status
gh run view                         # View latest run
gh run view 12345                   # View specific run
gh run view 12345 --web             # Open run in browser
gh run view 12345 --log             # View full logs
gh run view 12345 --log-failed      # View only failed logs

# Manage runs
gh run watch                        # Watch latest run
gh run watch 12345                  # Watch specific run
gh run rerun 12345                  # Rerun a workflow
gh run rerun 12345 --failed         # Rerun only failed jobs
gh run cancel 12345                 # Cancel a run
gh run download 12345               # Download artifacts

# Trigger workflows
gh workflow run build.yml           # Trigger workflow
gh workflow run build.yml --ref feature-branch
gh workflow run build.yml -f param=value  # With inputs
gh workflow list                    # List workflows
gh workflow view build.yml          # View workflow details
gh workflow enable build.yml        # Enable workflow
gh workflow disable build.yml       # Disable workflow
```

### Releases
```bash
# Create releases
gh release create v1.0.0            # Create release (interactive)
gh release create v1.0.0 --title "Release 1.0"
gh release create v1.0.0 --notes "Release notes here"
gh release create v1.0.0 --generate-notes  # Auto-generate notes
gh release create v1.0.0 --draft    # Create as draft
gh release create v1.0.0 --prerelease
gh release create v1.0.0 ./dist/*   # Upload assets

# List and view
gh release list                     # List releases
gh release view v1.0.0              # View release details
gh release view v1.0.0 --web        # Open in browser

# Manage releases
gh release download v1.0.0          # Download release assets
gh release download v1.0.0 --pattern "*.zip"
gh release edit v1.0.0 --draft=false  # Publish draft
gh release delete v1.0.0            # Delete release
gh release upload v1.0.0 ./file.zip # Upload additional asset
```

### Gists
```bash
gh gist create file.txt             # Create gist from file
gh gist create file1.txt file2.txt  # Multiple files
gh gist create --public file.txt    # Public gist
gh gist create -d "Description" file.txt
cat file.txt | gh gist create       # From stdin
gh gist list                        # List your gists
gh gist view <gist-id>              # View gist
gh gist view <gist-id> --raw        # View raw content
gh gist edit <gist-id>              # Edit gist
gh gist clone <gist-id>             # Clone gist
gh gist delete <gist-id>            # Delete gist
```

### Browse (Quick Open in Browser)
```bash
gh browse                           # Open repo in browser
gh browse --settings                # Open repo settings
gh browse --wiki                    # Open wiki
gh browse --projects                # Open projects
gh browse path/to/file.js           # Open specific file
gh browse path/to/file.js:42        # Open file at line 42
gh browse --branch feature          # Open specific branch
gh browse --commit abc123           # Open specific commit
```

### Search
```bash
# Search repos
gh search repos "language:go stars:>1000"
gh search repos "org:facebook react"
gh search repos --owner=username

# Search issues and PRs
gh search issues "bug label:urgent"
gh search issues "is:open is:issue author:@me"
gh search prs "is:open review-requested:@me"
gh search prs "is:merged author:@me"

# Search code
gh search code "className" --repo owner/repo
gh search code "TODO" --filename "*.js"

# Search commits
gh search commits "fix bug" --author username
```

### API (Direct GitHub API Access)
```bash
# GET requests
gh api repos/owner/repo             # Get repo info
gh api user                         # Get current user
gh api repos/owner/repo/pulls       # List PRs via API
gh api repos/owner/repo/issues/123/comments  # Get issue comments

# POST/PATCH requests
gh api repos/owner/repo/issues -f title="Title" -f body="Body"
gh api repos/owner/repo/issues/123 -X PATCH -f state="closed"

# GraphQL
gh api graphql -f query='{ viewer { login } }'

# Pagination
gh api repos/owner/repo/issues --paginate
```

### Configuration
```bash
gh config list                      # List all config
gh config get editor                # Get specific setting
gh config set editor vim            # Set editor
gh config set git_protocol ssh      # Use SSH for git operations
gh config set browser firefox       # Set default browser
gh config set prompt disabled       # Disable interactive prompts
```

### Aliases (Custom Commands)
```bash
gh alias set pv "pr view"           # Create alias
gh alias set co "pr checkout"
gh alias set mypr "pr list --author @me"
gh alias list                       # List all aliases
gh alias delete pv                  # Delete alias
```

### Extensions
```bash
gh extension list                   # List installed extensions
gh extension search                 # Search available extensions
gh extension install owner/gh-ext  # Install extension
gh extension upgrade owner/gh-ext  # Upgrade extension
gh extension remove owner/gh-ext   # Remove extension
```

### Useful Combinations
```bash
# Quick PR workflow
gh pr create --fill && gh pr merge --auto --squash

# Check CI and open PR if passing
gh pr checks --watch && gh browse

# Create issue from template
gh issue create --template bug_report.md

# Clone all repos from an org
gh repo list org-name --limit 100 --json name -q '.[].name' | xargs -I {} gh repo clone org-name/{}

# Watch latest workflow and notify
gh run watch && echo "Build complete!"
```

---

## Clone with Specific User Config

### Clone and set user immediately
```bash
# Clone and configure in one go
git clone https://github.com/user/repo.git && \
cd repo && \
git config user.name "Your Name" && \
git config user.email "your.email@example.com"
```

### Clone with custom config
```bash
# Method 1: Clone then configure
git clone https://github.com/user/repo.git
cd repo
git config user.name "John Doe"
git config user.email "john@example.com"

# Method 2: Clone with config in one line
git -c user.name="John Doe" -c user.email="john@example.com" clone https://github.com/user/repo.git

# Method 3: Using environment variables
GIT_AUTHOR_NAME="John Doe" \
GIT_AUTHOR_EMAIL="john@example.com" \
GIT_COMMITTER_NAME="John Doe" \
GIT_COMMITTER_EMAIL="john@example.com" \
git clone https://github.com/user/repo.git
```

### Setup different users for work/personal
```bash
# Personal projects
git config --global user.name "Personal Name"
git config --global user.email "personal@example.com"

# Work projects (override in specific repos)
cd work-project
git config user.name "Work Name"
git config user.email "work@company.com"

# Check current config
git config user.name
git config user.email
```

### Use SSH with different accounts
```bash
# ~/.ssh/config
Host github-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_personal

Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_work

# Clone using specific SSH config
git clone git@github-personal:username/repo.git
git clone git@github-work:company/repo.git
```

---

## Quick Tips

- **Undo almost anything**: Check `git reflog` first - it's a lifesaver!
- **Before force push**: Always create a backup branch
- **Testing destructive commands**: Use `--dry-run` flag when available
- **Scripting**: Use `--no-pager` and `--no-color` flags for clean output

---

*Last updated: 2025-12-10*
