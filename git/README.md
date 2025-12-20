# Git

> Version control that makes sense. From daily commands to "oh no" recovery.

## Start Here

| Guide | Description |
|-------|-------------|
| [Daily Workflow](daily-workflow.md) | status, add, commit, push, pull - the basics |
| [Branching](branching.md) | Create, switch, merge, rebase |

## When Things Go Wrong

| Guide | Description |
|-------|-------------|
| [Oops Recovery](oops-recovery.md) | "I accidentally..." - every mistake has a fix |

## Go Deeper

| Guide | Description |
|-------|-------------|
| [History](history.md) | log, blame, bisect - understand what happened |
| [Team Workflows](team-workflows.md) | Stash, worktrees, cherry-pick, collaboration |
| [Config](config.md) | .gitconfig, aliases, multiple accounts |
| [GitHub CLI](github-cli.md) | gh commands for PRs, issues, repos |

---

## Quick Navigation

**"I need to..."**

- [Undo my last commit](oops-recovery.md#undo-last-commit)
- [Fix a merge conflict](branching.md#resolve-conflicts)
- [See who changed a line](history.md#blame)
- [Create a pull request](github-cli.md#pull-requests)
- [Stash my changes](team-workflows.md#stash)

---

## Mental Model

Git has three areas:

```
Working Directory → Staging Area → Repository
    (your files)      (git add)     (git commit)
```

Everything else builds on this. See [Oops Recovery](oops-recovery.md) for why this matters when fixing mistakes.

---

*Not finding what you need? Check [GitHub CLI](github-cli.md) for GitHub-specific commands.*
