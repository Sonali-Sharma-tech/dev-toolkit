# Dev Toolkit - Developer Cheatsheet & Command Reference

[![GitHub stars](https://img.shields.io/github/stars/Sonali-Sharma-tech/dev-toolkit?style=flat)](https://github.com/Sonali-Sharma-tech/dev-toolkit/stargazers)
[![GitHub last commit](https://img.shields.io/github/last-commit/Sonali-Sharma-tech/dev-toolkit?style=flat)](https://github.com/Sonali-Sharma-tech/dev-toolkit/commits)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![GitHub issues](https://img.shields.io/github/issues/Sonali-Sharma-tech/dev-toolkit)](https://github.com/Sonali-Sharma-tech/dev-toolkit/issues)
[![GitHub forks](https://img.shields.io/github/forks/Sonali-Sharma-tech/dev-toolkit?style=flat)](https://github.com/Sonali-Sharma-tech/dev-toolkit/network)

A practical, no-fluff reference guide for developers. Commands, shortcuts, and cheatsheets you'll actually use daily.

> **No googling the same command twice.** Bookmark this and get back to coding.

---

## Quick Examples

```bash
# Undo last commit but keep changes
git reset --soft HEAD~1

# Find and kill process on port 3000
lsof -ti:3000 | xargs kill -9

# Search for text in all files recursively
grep -rn "searchterm" .
```

---

## What's Inside

> **Difficulty:** 🟢 Beginner | 🟡 Intermediate | 🔴 Advanced

| Section | Description | Level |
|---------|-------------|-------|
| [Git Commands](git/commands.md) | Version control from basics to advanced (rebase, cherry-pick, reflog) | 🟢🟡🔴 |
| [Terminal Cheatsheet](terminal/) | Bash one-liners, Zsh tips, productivity shortcuts | 🟢🟡 |
| [Vim/Neovim](editors/) | Essential editor commands and navigation | 🟡 |
| [Regex Patterns](regex/) | Real-world patterns, copy-paste ready | 🟡🔴 |
| [Docker & Kubernetes](containers/) | Container commands and debugging | 🟡🔴 |
| [npm/yarn/pnpm](package-managers/) | Package manager unified reference | 🟢🟡 |
| [Networking](networking/) | SSH, curl, API testing, debugging | 🟡🔴 |
| [Troubleshooting](troubleshooting/) | Common errors and how to fix them | 🟢🟡 |

---

## One-Liners You'll Actually Use

Copy, paste, done. No explanations needed.

```bash
# Kill process on port
lsof -ti:3000 | xargs kill -9

# Undo last commit, keep changes staged
git reset --soft HEAD~1

# Find large files in current directory
find . -type f -size +100M -exec ls -lh {} \;

# Delete all node_modules recursively
find . -name "node_modules" -type d -prune -exec rm -rf {} +

# Get your public IP
curl -s ifconfig.me

# Pretty print JSON
cat file.json | python3 -m json.tool

# Count lines of code (excluding node_modules)
find . -name '*.js' -not -path './node_modules/*' | xargs wc -l

# Quick HTTP server in current directory
python3 -m http.server 8000

# Search command history
history | grep "search_term"

# Disk usage of current directory
du -sh */ | sort -hr
```

> 💡 Want more? Check out [Terminal Cheatsheet](terminal/) for the full collection.

---

## Philosophy

- **Copy-paste ready** - Minimal placeholders, maximum usability
- **"When to use"** - Context for each command, not just syntax
- **Real examples** - Practical patterns from actual workflows
- **Cross-platform** - macOS/Linux differences noted where applicable

---

## Contributing

Found a useful command or hack? PRs welcome!

---

If this saved you time, consider giving it a star. It helps others find it too.

*Built for developers who value their time.*
