# Git Hooks

> Automate checks before commits and pushes. Catch problems early.

## The Problem

- Committing code that fails linting
- Pushing broken tests
- Forgetting to format code
- Inconsistent commit messages
- Accidentally committing secrets

## The Solution

Git hooks that run automatically before commits and pushes.

---

## Table of Contents
- [Quick Setup](#quick-setup)
- [Pre-Commit Hooks](#pre-commit-hooks)
- [Commit Message Hooks](#commit-message-hooks)
- [Pre-Push Hooks](#pre-push-hooks)
- [Using Husky (Recommended)](#using-husky-recommended)
- [Common Recipes](#common-recipes)

---

## Quick Setup

### Option 1: Husky (for JavaScript projects)

```bash
npm install --save-dev husky
npx husky init
```

### Option 2: Manual hooks

```bash
touch .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

---

## Pre-Commit Hooks

Runs before every commit.

### Basic Pre-Commit Hook

```bash
#!/bin/bash
# .git/hooks/pre-commit

set -e

echo "Running pre-commit checks..."

npm run lint
npm run format:check
npm test

echo "All checks passed!"
```

### Only Check Staged Files

```bash
#!/bin/bash
# .git/hooks/pre-commit

STAGED=$(git diff --cached --name-only --diff-filter=ACM | grep -E '\.(js|ts)$' || true)

if [ -z "$STAGED" ]; then
    exit 0
fi

echo "$STAGED" | xargs npx eslint --fix
echo "$STAGED" | xargs git add

echo "Pre-commit checks passed!"
```

### Check for Secrets

```bash
#!/bin/bash
# .git/hooks/pre-commit

PATTERNS=("password\s*=" "api_key" "AWS_ACCESS_KEY" "-----BEGIN PRIVATE")

for pattern in "${PATTERNS[@]}"; do
    if git diff --cached | grep -i "$pattern"; then
        echo "ERROR: Potential secret found: $pattern"
        exit 1
    fi
done
```

### Python Pre-Commit

```bash
#!/bin/bash
# .git/hooks/pre-commit

set -e
STAGED=$(git diff --cached --name-only | grep '\.py$' || true)

if [ -n "$STAGED" ]; then
    echo "$STAGED" | xargs black --check
    echo "$STAGED" | xargs flake8
fi
```

---

## Commit Message Hooks

### Enforce Conventional Commits

```bash
#!/bin/bash
# .git/hooks/commit-msg

COMMIT_MSG=$(cat "$1")
PATTERN="^(feat|fix|docs|style|refactor|test|chore)(\([a-z]+\))?: .{1,72}$"

if ! echo "$COMMIT_MSG" | head -1 | grep -qE "$PATTERN"; then
    echo "ERROR: Invalid commit message format."
    echo ""
    echo "Expected: <type>(<scope>): <description>"
    echo "Types: feat, fix, docs, style, refactor, test, chore"
    echo ""
    echo "Examples:"
    echo "  feat(auth): add login functionality"
    echo "  fix: resolve memory leak"
    exit 1
fi
```

### Add Branch Name to Commit

```bash
#!/bin/bash
# .git/hooks/prepare-commit-msg

BRANCH=$(git symbolic-ref --short HEAD 2>/dev/null)
TICKET=$(echo "$BRANCH" | grep -oE "[A-Z]+-[0-9]+" || true)

if [ -n "$TICKET" ] && ! grep -q "$TICKET" "$1"; then
    sed -i.bak "1s/^/[$TICKET] /" "$1"
fi
```

---

## Pre-Push Hooks

### Run Full Tests Before Push

```bash
#!/bin/bash
# .git/hooks/pre-push

set -e

echo "Running full test suite..."
npm test
npm run build

echo "All checks passed. Pushing..."
```

### Prevent Force Push to Main

```bash
#!/bin/bash
# .git/hooks/pre-push

while read local_ref local_sha remote_ref remote_sha; do
    if echo "$remote_ref" | grep -qE "refs/heads/(main|master)"; then
        if ! git merge-base --is-ancestor "$remote_sha" "$local_sha" 2>/dev/null; then
            echo "ERROR: Force pushing to main/master is not allowed."
            exit 1
        fi
    fi
done
```

---

## Using Husky (Recommended)

### Setup

```bash
npm install --save-dev husky lint-staged
npx husky init
```

### package.json

```json
{
  "scripts": {
    "prepare": "husky"
  },
  "lint-staged": {
    "*.{js,ts}": ["eslint --fix", "prettier --write"],
    "*.{json,md}": ["prettier --write"]
  }
}
```

### .husky/pre-commit

```bash
npx lint-staged
```

### .husky/commit-msg (with commitlint)

```bash
npm install --save-dev @commitlint/cli @commitlint/config-conventional
```

```bash
npx --no -- commitlint --edit $1
```

### Skip Hooks When Needed

```bash
git commit --no-verify -m "emergency fix"
git push --no-verify
```

---

## Common Recipes

### ESLint + Prettier (JavaScript)

```json
{
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": ["eslint --fix", "prettier --write"]
  }
}
```

### Black + Flake8 (Python)

```bash
#!/bin/bash
STAGED=$(git diff --cached --name-only | grep '\.py$' || true)
if [ -n "$STAGED" ]; then
    echo "$STAGED" | xargs black
    echo "$STAGED" | xargs flake8
    echo "$STAGED" | xargs git add
fi
```

### gofmt (Go)

```bash
#!/bin/bash
STAGED=$(git diff --cached --name-only | grep '\.go$' || true)
if [ -n "$STAGED" ]; then
    gofmt -w $STAGED
    git add $STAGED
fi
```

### Prevent Large Files

```bash
#!/bin/bash
MAX_SIZE=5000000  # 5MB

for file in $(git diff --cached --name-only); do
    if [ -f "$file" ]; then
        size=$(wc -c < "$file")
        if [ "$size" -gt "$MAX_SIZE" ]; then
            echo "ERROR: $file is too large"
            exit 1
        fi
    fi
done
```

### Prevent Debug Code

```bash
#!/bin/bash
FORBIDDEN=("console.log" "debugger" "binding.pry")

for pattern in "${FORBIDDEN[@]}"; do
    if git diff --cached | grep -q "$pattern"; then
        echo "ERROR: Found '$pattern' in staged changes"
        exit 1
    fi
done
```

---

## Troubleshooting

### Hook Not Running

```bash
# Make executable
chmod +x .git/hooks/pre-commit
```

### Husky Not Running

```bash
npx husky install
```

### Share Hooks with Team

```bash
# Use a tracked directory
mkdir .githooks
git config core.hooksPath .githooks
```

---

## Best Practices

1. **Keep pre-commit fast** (< 10 seconds)
2. **Only check staged files**
3. **Auto-fix when possible**
4. **Provide clear error messages**
5. **Allow bypass for emergencies** (`--no-verify`)

---

*Git hooks = catch problems before they become everyone's problem.*
