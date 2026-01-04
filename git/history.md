# Git History Commands

Navigate and understand your project's history like a detective.

## Table of Contents
- [git log](#git-log)
- [git diff](#git-diff)
- [git blame](#git-blame)
- [git bisect](#git-bisect)
- [git show](#git-show)
- [git shortlog](#git-shortlog)

---

## git log

View commit history with powerful filtering options.

### Basic Usage
```bash
# View commit history
git log

# Compact one-line format
git log --oneline

# Show last N commits
git log -5
git log --oneline -10

# Show with diff
git log -p

# Show stats (files changed, insertions, deletions)
git log --stat
```

### Formatting Output
```bash
# Custom format
git log --pretty=format:"%h - %an, %ar : %s"

# Format with colors
git log --pretty=format:"%C(yellow)%h%C(reset) - %C(green)%an%C(reset): %s"

# Graph view (visualize branches)
git log --graph --oneline

# Full graph with all branches
git log --graph --oneline --all --decorate

# Date formatting
git log --date=short --pretty=format:"%h %ad %s"
git log --date=relative --oneline
```

### Format Placeholders
| Placeholder | Description |
|-------------|-------------|
| `%H` | Full commit hash |
| `%h` | Short commit hash |
| `%an` | Author name |
| `%ae` | Author email |
| `%ad` | Author date |
| `%ar` | Author date, relative |
| `%cn` | Committer name |
| `%s` | Subject (commit message) |
| `%b` | Body |

### Filtering Commits
```bash
# By author
git log --author="John"
git log --author="john@example.com"

# By date range
git log --since="2024-01-01"
git log --after="2024-01-01"
git log --until="2024-06-01"
git log --before="2024-06-01"

# Last 2 weeks
git log --since="2 weeks ago"

# By commit message
git log --grep="fix"
git log --grep="bug" -i  # Case insensitive

# By file
git log -- path/to/file.js
git log --oneline -- "*.md"

# By directory
git log -- src/components/

# Commits that changed specific text
git log -S "functionName"  # Pickaxe search
git log -G "regex.*pattern"  # Regex search

# Merge commits only
git log --merges

# Exclude merge commits
git log --no-merges

# Commits between branches
git log main..feature-branch
git log feature-branch..main
```

### Comparing Branches
```bash
# Commits in feature not in main
git log main..feature --oneline

# Commits in either, not both
git log main...feature --oneline

# What will be merged
git log main..HEAD --oneline
```

### Useful Aliases
```bash
# Pretty log with graph
git config --global alias.lg "log --graph --oneline --decorate --all"

# Log with stats
git config --global alias.ls "log --oneline --stat"

# Recent commits
git config --global alias.recent "log --oneline -10"
```

---

## git diff

Compare changes between commits, branches, or files.

### Working Directory Changes
```bash
# Unstaged changes (working dir vs staging)
git diff

# Staged changes (staging vs last commit)
git diff --staged
git diff --cached  # Same as --staged

# All changes (working dir vs last commit)
git diff HEAD

# Specific file
git diff path/to/file.js
git diff --staged path/to/file.js
```

### Comparing Commits
```bash
# Between two commits
git diff abc123 def456

# Between commit and HEAD
git diff abc123 HEAD

# Last commit vs previous
git diff HEAD~1 HEAD

# Show only file names
git diff --name-only HEAD~1 HEAD

# Show names with status (A/M/D)
git diff --name-status HEAD~1 HEAD
```

### Comparing Branches
```bash
# Difference between branches
git diff main feature-branch

# What changed in feature since branching from main
git diff main...feature-branch

# Specific file between branches
git diff main:src/app.js feature:src/app.js
```

### Output Options
```bash
# Show word-level diff
git diff --word-diff

# Ignore whitespace
git diff -w
git diff --ignore-all-space

# Ignore blank lines
git diff --ignore-blank-lines

# Show only stats
git diff --stat

# Show short stat
git diff --shortstat

# Limit context lines
git diff -U3  # 3 lines of context (default)
git diff -U0  # No context, just changes
```

### Diff for Code Review
```bash
# Changes in a PR (commits in feature not in main)
git diff main...feature-branch

# What files changed
git diff --name-only main...feature-branch

# Diff with line numbers
git diff --no-prefix -U1000 | grep -n "^"
```

### External Diff Tools
```bash
# Use configured diff tool
git difftool

# Use specific tool
git difftool --tool=vimdiff
git difftool --tool=vscode

# Configure VS Code as diff tool
git config --global diff.tool vscode
git config --global difftool.vscode.cmd 'code --wait --diff $LOCAL $REMOTE'
```

---

## git blame

Find out who wrote each line of a file and when.

### Basic Usage
```bash
# Blame entire file
git blame path/to/file.js

# Blame specific lines
git blame -L 10,20 path/to/file.js

# Blame from line 10 to end
git blame -L 10, path/to/file.js

# Blame starting from a function
git blame -L :functionName path/to/file.js
```

### Output Formatting
```bash
# Show email instead of name
git blame -e path/to/file.js

# Show short hash
git blame --abbrev=7 path/to/file.js

# Suppress author name (just date)
git blame -s path/to/file.js

# Show line numbers from original
git blame -n path/to/file.js
```

### Ignoring Whitespace and Moves
```bash
# Ignore whitespace changes
git blame -w path/to/file.js

# Detect lines moved within file
git blame -M path/to/file.js

# Detect lines copied from other files
git blame -C path/to/file.js

# More aggressive copy detection
git blame -CCC path/to/file.js
```

### Blame at Specific Revision
```bash
# Blame at specific commit
git blame abc123 -- path/to/file.js

# Blame before specific commit
git blame abc123^ -- path/to/file.js

# Blame at a tag
git blame v1.0.0 -- path/to/file.js
```

### Blame Ignore Revisions
```bash
# Ignore specific commit (formatting changes, etc.)
git blame --ignore-rev abc123 path/to/file.js

# Ignore commits listed in file
echo "abc123" >> .git-blame-ignore-revs
git blame --ignore-revs-file .git-blame-ignore-revs path/to/file.js

# Configure to always use ignore file
git config blame.ignoreRevsFile .git-blame-ignore-revs
```

> **Tip**: Create `.git-blame-ignore-revs` with commit hashes of large formatting changes or linting fixes. GitHub recognizes this file.

---

## git bisect

Binary search to find the commit that introduced a bug.

### Basic Workflow
```bash
# Start bisecting
git bisect start

# Mark current commit as bad (has the bug)
git bisect bad

# Mark known good commit (before the bug)
git bisect good abc123

# Git checks out middle commit - test it, then:
git bisect good  # If bug not present
git bisect bad   # If bug present

# Repeat until git finds the culprit
# Git will output: "abc123 is the first bad commit"

# Done - return to original branch
git bisect reset
```

### Bisect with Tags or Branches
```bash
git bisect start
git bisect bad HEAD
git bisect good v1.0.0

# Or with branch names
git bisect start HEAD main~50
```

### Automated Bisect
```bash
# Run a test script at each step
git bisect start HEAD abc123
git bisect run npm test

# Run specific test
git bisect run npm test -- --grep "login"

# Run custom script
git bisect run ./test-for-bug.sh
```

### Script Exit Codes
| Exit Code | Meaning |
|-----------|---------|
| 0 | Good (bug not present) |
| 1-124, 126-127 | Bad (bug present) |
| 125 | Skip (can't test this commit) |

### Skip Untestable Commits
```bash
# If a commit doesn't compile or can't be tested
git bisect skip

# Skip a range
git bisect skip abc123..def456
```

### Bisect Log and Replay
```bash
# Show bisect log
git bisect log

# Save log to file
git bisect log > bisect.log

# Replay a bisect session
git bisect replay bisect.log
```

### Visualize Bisect
```bash
# Show remaining commits to test
git bisect visualize

# With gitk
git bisect visualize --all
```

### Example: Finding a Performance Regression
```bash
# Create test script: test-perf.sh
#!/bin/bash
npm run build
time=$(node -e "console.time('t'); require('./dist'); console.timeEnd('t')" 2>&1)
ms=$(echo $time | grep -oP '\d+')
[ $ms -lt 100 ] && exit 0 || exit 1

# Run bisect
chmod +x test-perf.sh
git bisect start HEAD v1.0.0
git bisect run ./test-perf.sh
```

---

## git show

Display information about commits, tags, or other objects.

### Show Commits
```bash
# Show latest commit with diff
git show

# Show specific commit
git show abc123

# Show commit without diff
git show --stat abc123
git show --name-only abc123

# Show only commit message
git show -s abc123
git show --no-patch abc123

# Show specific file at commit
git show abc123:path/to/file.js

# Show file from N commits ago
git show HEAD~3:path/to/file.js
```

### Show Tags
```bash
# Show annotated tag details
git show v1.0.0

# Show tag message only
git show -s v1.0.0
```

### Show Multiple Objects
```bash
# Show multiple commits
git show abc123 def456

# Show range
git show HEAD~3..HEAD
```

### Format Options
```bash
# Custom format
git show --format="%h %s" --no-patch

# Pretty formats
git show --pretty=oneline --no-patch
git show --pretty=short --no-patch
git show --pretty=full --no-patch
```

---

## git shortlog

Summarize commit history by author.

### Basic Usage
```bash
# Group commits by author
git shortlog

# Count only (no commit messages)
git shortlog -s

# Sort by commit count
git shortlog -sn

# Include email
git shortlog -sne
```

### Filtering
```bash
# Last 100 commits
git shortlog -sn HEAD~100..HEAD

# Specific time period
git shortlog -sn --since="2024-01-01" --until="2024-06-01"

# Specific branch
git shortlog -sn main

# Exclude merges
git shortlog -sn --no-merges
```

### By File or Directory
```bash
# Who worked on specific file
git shortlog -sn -- path/to/file.js

# Who worked on directory
git shortlog -sn -- src/components/
```

### Generate Contributors List
```bash
# For README
git shortlog -sn --no-merges | head -20

# Formatted for markdown
git shortlog -sn --no-merges | awk '{print "- "$2" "$3" ("$1" commits)"}'
```

---

## Quick Reference

| Task | Command |
|------|---------|
| View history | `git log --oneline` |
| History with graph | `git log --graph --oneline --all` |
| Search commits | `git log --grep="search term"` |
| Find code addition | `git log -S "code"` |
| Compare branches | `git diff main..feature` |
| See staged changes | `git diff --staged` |
| Who wrote this line | `git blame file.js` |
| Find bug introduction | `git bisect start` |
| Show commit details | `git show abc123` |
| Top contributors | `git shortlog -sn` |

