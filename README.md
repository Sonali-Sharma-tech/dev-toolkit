# Dev Toolkit

> Commands you look up more than once belong in a cheatsheet.

[![Cheatsheets](https://img.shields.io/badge/Cheatsheets-55%20files-blue)](https://github.com/Sonali-Sharma-tech/dev-toolkit)
[![Last Commit](https://img.shields.io/github/last-commit/Sonali-Sharma-tech/dev-toolkit?style=flat)](https://github.com/Sonali-Sharma-tech/dev-toolkit/commits)
[![Platform](https://img.shields.io/badge/Platform-macOS%20%7C%20Linux-lightgrey)](https://github.com/Sonali-Sharma-tech/dev-toolkit)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

A practical reference for daily dev work — not docs, not tutorials.
Copy-paste ready commands with context for *when* to use them.

---

## Jump To

| I want to... | Go here |
|---|---|
| Undo something in git | [git/oops-recovery.md](git/oops-recovery.md) |
| Review history / find a commit | [git/history.md](git/history.md) |
| Set up git config properly | [git/config.md](git/config.md) |
| Work on a team branch workflow | [git/team-workflows.md](git/team-workflows.md) |
| Debug a network/curl issue | [networking/debugging.md](networking/debugging.md) |
| Master curl | [networking/curl-essentials.md](networking/curl-essentials.md) |
| SSH setup and shortcuts | [networking/ssh-mastery.md](networking/ssh-mastery.md) |
| Survive vim | [editors/vim-survival.md](editors/vim-survival.md) |
| Clean up Docker | [containers/docker-cleanup.md](containers/docker-cleanup.md) |
| Fix npm / Homebrew issues | [package-managers/troubleshooting.md](package-managers/troubleshooting.md) |

---

## One-Liners

```bash
# Undo last commit, keep changes staged
git reset --soft HEAD~1

# Undo a pushed commit safely
git revert HEAD && git push

# Scrub a secret from ALL git history
brew install git-filter-repo
echo "real_secret==>REDACTED" > replacements.txt
git filter-repo --replace-text replacements.txt

# Kill process on port 3000
lsof -ti:3000 | xargs kill -9

# Find large files
find . -type f -size +100M -exec ls -lh {} \;

# Delete all node_modules recursively
find . -name "node_modules" -type d -prune -exec rm -rf {} +

# Quick HTTP server
python3 -m http.server 8000
```

---

## What's Inside

```
git/               oops recovery, history, branching, team workflows, config
networking/        curl, SSH, debugging flowcharts, DNS, commands
editors/           vim survival → productive → full config
containers/        docker, compose, cleanup
package-managers/  npm, yarn/pnpm, homebrew, troubleshooting
```

---

## Philosophy

- **Context over syntax** — every command explains *when*, not just *how*
- **Warnings where it matters** — destructive commands are clearly labelled
- **No outdated commands** — deprecated flags are marked, modern alternatives shown

---

*Built for developers who'd rather ship than google.*
