# Git Configuration

Customize Git to work exactly how you want.

## Table of Contents
- [Configuration Basics](#configuration-basics)
- [Essential Settings](#essential-settings)
- [Useful Aliases](#useful-aliases)
- [.gitconfig Examples](#gitconfig-examples)
- [Git Hooks](#git-hooks)
- [.gitignore Patterns](#gitignore-patterns)
- [.gitattributes](#gitattributes)

---

## Configuration Basics

### Config Levels
```bash
# System (all users)
git config --system key value
# Location: /etc/gitconfig

# Global (your user)
git config --global key value
# Location: ~/.gitconfig

# Local (current repo only)
git config --local key value
# Location: .git/config

# Config precedence: local > global > system
```

### View Configuration
```bash
# Show all config
git config --list

# Show with origin (which file set it)
git config --list --show-origin

# Show specific value
git config user.name

# Show global config only
git config --global --list

# Edit config in editor
git config --global --edit
```

### Set Configuration
```bash
# Set value
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Unset value
git config --global --unset key

# Add to multi-value key
git config --add key value
```

---

## Essential Settings

### Identity
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Per-repo email (for work vs personal)
git config user.email "work@company.com"
```

### Default Editor
```bash
# VS Code
git config --global core.editor "code --wait"

# Vim
git config --global core.editor "vim"

# Nano
git config --global core.editor "nano"

# Sublime Text
git config --global core.editor "subl -n -w"
```

### Default Branch
```bash
# Set default branch name for new repos
git config --global init.defaultBranch main
```

### Pull Behavior
```bash
# Rebase instead of merge on pull
git config --global pull.rebase true

# Only allow fast-forward pulls
git config --global pull.ff only
```

### Push Behavior
```bash
# Push current branch to same-named remote
git config --global push.default current

# Auto setup remote tracking
git config --global push.autoSetupRemote true
```

### Merge and Diff
```bash
# Use VS Code for merges
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'

# Use VS Code for diffs
git config --global diff.tool vscode
git config --global difftool.vscode.cmd 'code --wait --diff $LOCAL $REMOTE'

# Show original in conflict markers
git config --global merge.conflictstyle diff3
```

### Colors
```bash
# Enable colors (usually default)
git config --global color.ui auto

# Customize colors
git config --global color.status.changed "yellow"
git config --global color.status.untracked "red"
```

### Line Endings
```bash
# On macOS/Linux
git config --global core.autocrlf input

# On Windows
git config --global core.autocrlf true

# Warn about line ending issues
git config --global core.safecrlf warn
```

### Credentials
```bash
# Cache credentials in memory (default 15 min)
git config --global credential.helper cache

# Cache for 1 hour
git config --global credential.helper 'cache --timeout=3600'

# Use macOS Keychain
git config --global credential.helper osxkeychain

# Use Windows Credential Manager
git config --global credential.helper wincred
```

---

## Useful Aliases

### Basic Shortcuts
```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.cp cherry-pick
```

### Status and Log
```bash
# Short status
git config --global alias.s "status -sb"

# Pretty log
git config --global alias.lg "log --graph --oneline --decorate"

# Log with all branches
git config --global alias.lga "log --graph --oneline --decorate --all"

# Recent commits
git config --global alias.recent "log --oneline -10"

# Log today's commits
git config --global alias.today "log --since=midnight --oneline"
```

### Diff Aliases
```bash
# Staged diff
git config --global alias.ds "diff --staged"

# Word diff
git config --global alias.dw "diff --word-diff"

# Diff stats only
git config --global alias.stat "diff --stat"
```

### Branch Management
```bash
# List branches sorted by last commit
git config --global alias.brs "branch --sort=-committerdate --format='%(committerdate:relative)%09%(refname:short)'"

# Delete merged branches
git config --global alias.cleanup "!git branch --merged main | grep -v main | xargs -n 1 git branch -d"

# Switch to previous branch
git config --global alias.prev "checkout -"
```

### Undo Aliases
```bash
# Unstage everything
git config --global alias.unstage "reset HEAD --"

# Undo last commit (keep changes)
git config --global alias.undo "reset --soft HEAD~1"

# Discard changes in file
git config --global alias.discard "checkout --"

# Amend without changing message
git config --global alias.amend "commit --amend --no-edit"
```

### Workflow Aliases
```bash
# Add all and commit
git config --global alias.ac "!git add -A && git commit -m"

# Push with upstream set
git config --global alias.pushu "push -u origin HEAD"

# Pull with rebase
git config --global alias.pullr "pull --rebase"

# Sync with main (fetch + rebase)
git config --global alias.sync "!git fetch origin main && git rebase origin/main"
```

### Info Aliases
```bash
# Show contributors
git config --global alias.who "shortlog -sn --no-merges"

# Show last commit
git config --global alias.last "log -1 --stat"

# Show aliases
git config --global alias.aliases "config --get-regexp ^alias\\."
```

---

## .gitconfig Examples

### Minimal .gitconfig
```ini
[user]
    name = Your Name
    email = you@example.com

[init]
    defaultBranch = main

[pull]
    rebase = true

[core]
    editor = code --wait

[alias]
    st = status -sb
    co = checkout
    br = branch
    ci = commit
    lg = log --graph --oneline --decorate --all
```

### Full-Featured .gitconfig
```ini
[user]
    name = Your Name
    email = you@example.com
    signingkey = YOUR_GPG_KEY

[init]
    defaultBranch = main

[core]
    editor = code --wait
    autocrlf = input
    excludesfile = ~/.gitignore_global
    pager = delta  # Better diff viewer

[pull]
    rebase = true

[push]
    default = current
    autoSetupRemote = true

[merge]
    conflictstyle = diff3
    tool = vscode

[mergetool "vscode"]
    cmd = code --wait $MERGED

[diff]
    tool = vscode
    colorMoved = default

[difftool "vscode"]
    cmd = code --wait --diff $LOCAL $REMOTE

[commit]
    gpgsign = true

[tag]
    gpgsign = true

[credential]
    helper = osxkeychain

[color]
    ui = auto

[color "status"]
    added = green
    changed = yellow
    untracked = red

[alias]
    # Status
    s = status -sb
    st = status

    # Commits
    ci = commit
    amend = commit --amend --no-edit
    undo = reset --soft HEAD~1

    # Branches
    co = checkout
    br = branch
    brs = branch --sort=-committerdate --format='%(committerdate:relative)%09%(refname:short)'
    prev = checkout -

    # Logging
    lg = log --graph --oneline --decorate
    lga = log --graph --oneline --decorate --all
    recent = log --oneline -10
    today = log --since=midnight --oneline
    last = log -1 --stat

    # Diffs
    ds = diff --staged
    dw = diff --word-diff

    # Workflow
    sync = !git fetch origin main && git rebase origin/main
    cleanup = !git branch --merged main | grep -v main | xargs -n 1 git branch -d

    # Info
    who = shortlog -sn --no-merges
    aliases = config --get-regexp ^alias\\.

[url "git@github.com:"]
    insteadOf = https://github.com/

[filter "lfs"]
    clean = git-lfs clean -- %f
    smudge = git-lfs smudge -- %f
    process = git-lfs filter-process
    required = true
```

---

## Git Hooks

### Hook Location
```bash
# Hooks are in .git/hooks/
ls .git/hooks/

# Sample hooks end in .sample
# Remove .sample to activate

# Make hook executable
chmod +x .git/hooks/pre-commit
```

### Common Hooks

#### pre-commit
Runs before commit is created. Exit non-zero to abort.

```bash
#!/bin/bash
# .git/hooks/pre-commit

# Run linter
npm run lint
if [ $? -ne 0 ]; then
    echo "❌ Lint failed. Fix errors before committing."
    exit 1
fi

# Check for debug statements
if git diff --cached | grep -E "console\.(log|debug)" > /dev/null; then
    echo "❌ Remove console.log statements before committing."
    exit 1
fi

# Check for secrets
if git diff --cached | grep -iE "(password|secret|api_key)\s*=" > /dev/null; then
    echo "❌ Possible secrets detected. Review your changes."
    exit 1
fi

echo "✅ Pre-commit checks passed"
```

#### commit-msg
Validate or modify commit message.

```bash
#!/bin/bash
# .git/hooks/commit-msg

MSG_FILE=$1
MSG=$(cat "$MSG_FILE")

# Require minimum length
if [ ${#MSG} -lt 10 ]; then
    echo "❌ Commit message too short (min 10 chars)"
    exit 1
fi

# Require conventional commit format
if ! echo "$MSG" | grep -qE "^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .+"; then
    echo "❌ Use conventional commit format:"
    echo "   feat: add new feature"
    echo "   fix: bug fix"
    echo "   docs: documentation"
    exit 1
fi
```

#### pre-push
Runs before push. Abort if tests fail.

```bash
#!/bin/bash
# .git/hooks/pre-push

echo "Running tests before push..."
npm test
if [ $? -ne 0 ]; then
    echo "❌ Tests failed. Push aborted."
    exit 1
fi

echo "✅ Tests passed. Pushing..."
```

#### post-checkout
Runs after checkout. Useful for setup tasks.

```bash
#!/bin/bash
# .git/hooks/post-checkout

# Install dependencies if package.json changed
PREV_HEAD=$1
NEW_HEAD=$2

if git diff --name-only $PREV_HEAD $NEW_HEAD | grep -q "package.json"; then
    echo "📦 package.json changed. Running npm install..."
    npm install
fi
```

### Share Hooks with Team

```bash
# Store hooks in repo
mkdir .githooks
cp .git/hooks/pre-commit .githooks/

# Tell git to use this directory
git config core.hooksPath .githooks

# Or use a tool like Husky
npx husky-init
npm install
```

### Husky Setup (Recommended)
```bash
# Install
npm install husky --save-dev
npx husky init

# Add pre-commit hook
echo "npm run lint" > .husky/pre-commit

# Add commit-msg hook for conventional commits
npm install @commitlint/cli @commitlint/config-conventional --save-dev
echo "npx --no -- commitlint --edit \$1" > .husky/commit-msg
```

---

## .gitignore Patterns

### Syntax
```gitignore
# Comment
file.txt          # Specific file
*.log             # All .log files
/build            # build in root only
build/            # build directory anywhere
!important.log    # Exception (don't ignore)
**/logs           # logs dir at any depth
*.py[cod]         # *.pyc, *.pyo, *.pyd
```

### Global .gitignore
```bash
# Create global ignore file
touch ~/.gitignore_global

# Tell git to use it
git config --global core.excludesfile ~/.gitignore_global
```

### Common Patterns

```gitignore
# OS files
.DS_Store
Thumbs.db
*.swp
*~

# Editor/IDE
.idea/
.vscode/
*.sublime-*

# Dependencies
node_modules/
vendor/
venv/
__pycache__/

# Build output
dist/
build/
*.o
*.pyc

# Logs
*.log
logs/
npm-debug.log*

# Environment
.env
.env.local
.env.*.local

# Secrets
*.pem
*.key
credentials.json

# Test coverage
coverage/
.nyc_output/

# Package managers
package-lock.json  # Optional
yarn.lock          # Optional

# Misc
.cache/
tmp/
*.tmp
```

### Per-repo Ignore (Without .gitignore)
```bash
# Add to .git/info/exclude
echo "my-local-file.txt" >> .git/info/exclude
```

---

## .gitattributes

Control how Git handles files.

### Basic .gitattributes
```gitattributes
# Auto detect text files
* text=auto

# Force specific file types
*.sh text eol=lf
*.bat text eol=crlf
*.md text
*.json text

# Binary files
*.png binary
*.jpg binary
*.pdf binary
*.zip binary

# Prevent merge conflicts in lockfiles
package-lock.json merge=ours
yarn.lock merge=ours
```

### Diff Settings
```gitattributes
# Better diffs for specific files
*.md diff=markdown
*.css diff=css
*.html diff=html
*.php diff=php
```

### LFS (Large File Storage)
```gitattributes
# Track large files with Git LFS
*.psd filter=lfs diff=lfs merge=lfs -text
*.zip filter=lfs diff=lfs merge=lfs -text
*.mp4 filter=lfs diff=lfs merge=lfs -text
```

### Export Ignore
```gitattributes
# Exclude from git archive exports
.gitattributes export-ignore
.gitignore export-ignore
.github/ export-ignore
tests/ export-ignore
```

---

## Quick Reference

| Task | Command |
|------|---------|
| Set username | `git config --global user.name "Name"` |
| Set email | `git config --global user.email "email"` |
| Set editor | `git config --global core.editor "code --wait"` |
| Set default branch | `git config --global init.defaultBranch main` |
| View all config | `git config --list` |
| Edit config | `git config --global --edit` |
| Create alias | `git config --global alias.co checkout` |
| Set hooks path | `git config core.hooksPath .githooks` |

