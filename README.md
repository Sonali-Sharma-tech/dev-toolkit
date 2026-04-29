# Dev Toolkit

> Commands you look up more than once belong in a cheatsheet.

[![Cheatsheets](https://img.shields.io/badge/Cheatsheets-58%20files-blue)](https://github.com/Sonali-Sharma-tech/dev-toolkit)
[![Last Commit](https://img.shields.io/github/last-commit/Sonali-Sharma-tech/dev-toolkit?style=flat)](https://github.com/Sonali-Sharma-tech/dev-toolkit/commits)
[![Platform](https://img.shields.io/badge/Platform-macOS%20%7C%20Linux-lightgrey)](https://github.com/Sonali-Sharma-tech/dev-toolkit)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

A practical reference for daily dev work — not docs, not tutorials. Copy-paste ready commands with context for *when* to use them.

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
| Survive vim (or actually use it) | [editors/vim-survival.md](editors/vim-survival.md) |
| Clean up Docker mess | [containers/docker-cleanup.md](containers/docker-cleanup.md) |
| Fix npm / Homebrew issues | [package-managers/troubleshooting.md](package-managers/troubleshooting.md) |

---

## One-Liners

```bash
# Undo last commit, keep changes staged
git reset --soft HEAD~1

# Undo last PUSHED commit (safe — creates a revert commit)
git revert HEAD && git push

# Scrub a secret from ALL git history (modern approach)
brew install git-filter-repo
echo "real_secret==>REDACTED" > replacements.txt
git filter-repo --replace-text replacements.txt

# Kill process on port 3000
lsof -ti:3000 | xargs kill -9

# Find large files
find . -type f -size +100M -exec ls -lh {} \;

# Delete all node_modules recursively
find . -name "node_modules" -type d -prune -exec rm -rf {} +

# Pretty print JSON
cat file.json | python3 -m json.tool

# Quick HTTP server
python3 -m http.server 8000

# Count lines of code (excluding node_modules)
find . -name '*.js' -not -path './node_modules/*' | xargs wc -l

# Disk usage sorted by size
du -sh */ | sort -hr
```

---

## What's Inside

```
git/
  ├── oops-recovery.md     ← committed secrets, wrong branch, lost commits
  ├── history.md           ← log, blame, bisect, diff
  ├── branching.md         ← strategies, naming, rebasing
  ├── team-workflows.md    ← PR flow, code review, merge strategies
  ├── config.md            ← aliases, hooks, global setup
  ├── commands.md          ← quick reference
  └── daily-workflow.md    ← morning → commit → push ritual

networking/
  ├── curl-essentials.md   ← auth, headers, debugging, SSL
  ├── ssh-mastery.md       ← keys, config, tunnels, agent forwarding
  ├── debugging.md         ← flowcharts for common failures
  ├── commands.md          ← netstat, dig, nmap, openssl
  └── dns-ports.md         ← common ports, DNS resolution

editors/
  ├── vim-survival.md      ← minimum to not get stuck
  ├── vim-productive.md    ← motions, macros, registers
  ├── vim-config.md        ← .vimrc setup
  └── vim.md               ← full reference

containers/
  ├── docker.md            ← images, containers, volumes
  ├── docker-compose.md    ← services, networks, overrides
  └── docker-cleanup.md    ← prune everything safely

package-managers/
  ├── npm-essentials.md    ← install, audit, scripts, workspaces
  ├── yarn-pnpm.md         ← when to use which
  ├── homebrew.md          ← install, update, doctor
  └── troubleshooting.md  ← cache issues, permission errors
```

---

## Philosophy

- **Context over syntax** — every command explains *when*, not just *how*
- **Warnings where it matters** — destructive commands are labelled clearly
- **No outdated commands** — deprecated flags are marked, modern alternatives shown
- **Cross-platform** — macOS/Linux differences noted where they exist

---

## Contributing

Found a command you look up more than once? [Open a PR](CONTRIBUTING.md) — format it like the existing entries (command + when to use it + any gotchas).

---

*Built for developers who'd rather ship than google.*
