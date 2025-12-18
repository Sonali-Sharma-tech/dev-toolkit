# Dev Toolkit - Developer Cheatsheet & Command Reference

[![GitHub stars](https://img.shields.io/github/stars/Sonali-Sharma-tech/dev-toolkit?style=flat)](https://github.com/Sonali-Sharma-tech/dev-toolkit/stargazers)
[![GitHub last commit](https://img.shields.io/github/last-commit/Sonali-Sharma-tech/dev-toolkit?style=flat)](https://github.com/Sonali-Sharma-tech/dev-toolkit/commits)

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

| Section | Description |
|---------|-------------|
| [Git Commands](git/commands.md) | Version control from basics to advanced (rebase, cherry-pick, reflog) |
| [Terminal Cheatsheet](terminal/) | Bash one-liners, Zsh tips, productivity shortcuts |
| [Vim/Neovim](editors/) | Essential editor commands and navigation |
| [Regex Patterns](regex/) | Real-world patterns, copy-paste ready |
| [Docker & Kubernetes](containers/) | Container commands and debugging |
| [npm/yarn/pnpm](package-managers/) | Package manager unified reference |
| [Networking](networking/) | SSH, curl, API testing, debugging |
| [Troubleshooting](troubleshooting/) | Common errors and how to fix them |

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
