# Git Branching

> Create, switch, merge, and manage branches.

---

## Create & Switch

```bash
# Create and switch to new branch
git checkout -b feature-name

# Or (Git 2.23+)
git switch -c feature-name

# Create branch from specific commit
git checkout -b hotfix abc1234

# Create branch without switching
git branch feature-name
```

---

## Switch Branches

```bash
git checkout main
git switch main              # Git 2.23+

git checkout -               # Previous branch
```

---

## List Branches

```bash
git branch                   # Local branches
git branch -a                # All (local + remote)
git branch -r                # Remote only
git branch -v                # With last commit
git branch --merged          # Merged into current
git branch --no-merged       # Not yet merged
```

---

## Delete Branches

```bash
# Local
git branch -d feature        # Safe delete (checks if merged)
git branch -D feature        # Force delete

# Remote
git push origin --delete feature
```

### Clean up merged branches

```bash
git branch --merged | grep -v '\*\|main\|master' | xargs -n 1 git branch -d
```

---

## Merge

```bash
# Merge feature into main
git checkout main
git merge feature

# Merge with commit (even for fast-forward)
git merge --no-ff feature
```

### Resolve Conflicts

When you see conflict markers:

```
<<<<<<< HEAD
your changes
=======
their changes
>>>>>>> feature
```

1. Edit the file to keep what you want
2. Remove the conflict markers
3. Stage and commit:

```bash
git add conflicted-file.txt
git commit -m "Resolve merge conflict"
```

### Abort Merge

```bash
git merge --abort
```

---

## Rebase

Replay your commits on top of another branch (cleaner history).

```bash
# Rebase current branch onto main
git checkout feature
git rebase main

# Or from any branch
git rebase main feature
```

### During Rebase

```bash
git rebase --continue        # After resolving conflicts
git rebase --abort           # Cancel and go back
git rebase --skip            # Skip current commit
```

### When to Use

| Merge | Rebase |
|-------|--------|
| Preserves complete history | Creates linear history |
| Shows when branches joined | Cleaner log |
| Safe for shared branches | **Never rebase pushed commits** |

### Interactive Rebase

Clean up commits before pushing:

```bash
git rebase -i HEAD~3         # Last 3 commits
```

Options:
- `pick` - keep commit
- `squash` - combine with previous
- `reword` - change message
- `drop` - remove commit

---

## Rename Branch

```bash
# Rename current branch
git branch -m new-name

# Rename any branch
git branch -m old-name new-name

# Update remote
git push origin -u new-name
git push origin --delete old-name
```

---

## Track Remote Branch

```bash
# Set upstream for current branch
git branch -u origin/feature

# Push and set upstream
git push -u origin feature

# See tracking info
git branch -vv
```

---

## Compare Branches

```bash
# Commits in feature not in main
git log main..feature --oneline

# Diff between branches
git diff main..feature

# Files changed
git diff main..feature --name-only
```

---

## Common Patterns

### Feature Branch Workflow

```bash
git checkout main
git pull
git checkout -b feature/new-thing

# ... work ...

git add .
git commit -m "Add new thing"
git push -u origin feature/new-thing

# Create PR, merge, then cleanup:
git checkout main
git pull
git branch -d feature/new-thing
```

### Keep Branch Updated

```bash
# On your feature branch
git fetch origin
git rebase origin/main

# Or merge
git merge origin/main
```

---

*Made a mistake? See [Oops Recovery](oops-recovery.md).*
