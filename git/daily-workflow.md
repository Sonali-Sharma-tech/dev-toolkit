# Git Daily Workflow

> The commands you'll use every day.

---

## Check Status

```bash
git status                    # What's changed?
git status -s                 # Short format
```

---

## View Changes

```bash
git diff                      # Unstaged changes
git diff --staged             # Staged changes (what will be committed)
git diff HEAD                 # All changes (staged + unstaged)
```

---

## Stage Changes

```bash
git add file.txt              # Stage specific file
git add .                     # Stage all changes
git add -p                    # Stage interactively (pick hunks)
```

### Unstage

```bash
git reset file.txt            # Unstage file (keep changes)
git reset                     # Unstage everything
```

---

## Commit

```bash
git commit -m "Add login feature"
git commit                    # Opens editor for message
git commit -am "Quick fix"    # Add all tracked + commit (skip staging)
```

### Good Commit Messages

```
feat: Add user authentication
fix: Resolve login timeout issue
docs: Update API documentation
refactor: Simplify database queries
```

---

## Push

```bash
git push                      # Push to tracked branch
git push origin main          # Push to specific branch
git push -u origin feature    # Push and set upstream (first time)
```

---

## Pull

```bash
git pull                      # Fetch + merge
git pull --rebase             # Fetch + rebase (cleaner history)
git fetch                     # Just fetch (don't merge)
```

### Pull vs Fetch

```bash
git pull origin main
# Same as:
git fetch origin main
git merge origin/main
```

Use `fetch` when you want to see what's new before merging.

---

## Common Workflow

### Starting work

```bash
git pull                      # Get latest
git checkout -b my-feature    # Create branch
```

### During work

```bash
git status                    # Check what's changed
git add .                     # Stage changes
git commit -m "WIP: feature"  # Commit
```

### Finishing work

```bash
git push -u origin my-feature # Push branch
# Create PR on GitHub
```

---

## Quick Patterns

```bash
# Discard all local changes
git checkout -- .

# See last commit
git log -1

# See what you're about to push
git log origin/main..HEAD

# See what you're about to pull
git fetch && git log HEAD..origin/main
```

---

*For branching and merging, see [Branching](branching.md). Made a mistake? See [Oops Recovery](oops-recovery.md).*
