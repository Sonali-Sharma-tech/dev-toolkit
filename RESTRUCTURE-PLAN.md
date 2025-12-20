# Dev-Toolkit Restructure Plan

> **Philosophy**: "Not everything. Just what matters."
> **Tagline**: "The commands senior devs actually use. No fluff."

---

## Executive Summary

Transform dev-toolkit from an exhaustive reference into an **opinionated, problem-first resource** that developers actually bookmark.

### Key Changes
1. **Reduce volume by ~60%** - Keep only what senior devs actually use daily
2. **Problem-first organization** - "I need to..." not "Here's a command..."
3. **Consistent pattern** - Essentials → Recipes → Oops/Troubleshooting
4. **Unique value-adds** - Things competitors don't have

---

## New Repository Structure

```
dev-toolkit/
├── README.md                    # Updated with new structure
├── CLAUDE.md                    # Project instructions (keep)
├── CONTRIBUTING.md              # Already created
├── RESTRUCTURE-PLAN.md          # This file (delete after complete)
│
├── terminal/
│   ├── README.md                # Section overview + quick links
│   ├── essentials.md            # 50 commands you'll use daily
│   ├── recipes/
│   │   ├── find-things.md       # Finding files, text, processes
│   │   ├── text-manipulation.md # awk, sed, cut, sort, uniq
│   │   ├── process-control.md   # ps, kill, jobs, bg/fg
│   │   └── disk-cleanup.md      # du, df, ncdu, cleanup scripts
│   ├── productivity/
│   │   ├── shell-setup.md       # Zsh + Oh-My-Zsh config
│   │   └── aliases.md           # Curated aliases that save time
│   ├── scripting.md             # Bash scripting essentials only
│   └── one-liners.md            # Top 30 (down from 1200+ lines)
│
├── git/
│   ├── README.md                # Section overview
│   ├── daily-workflow.md        # add, commit, push, pull, status
│   ├── branching.md             # Branches, merging, rebasing
│   ├── oops-recovery.md         # 🔥 "I accidentally..." solutions
│   ├── history.md               # log, diff, blame, bisect
│   ├── team-workflows.md        # PRs, conflicts, cherry-pick
│   └── config.md                # .gitconfig, aliases, hooks
│
├── regex/
│   ├── README.md                # When to use, quick reference
│   ├── basics.md                # Core syntax, cheat table
│   ├── recipes.md               # Copy-paste patterns with tests
│   └── by-language.md           # JS vs Python vs grep differences
│
├── editors/
│   ├── README.md
│   ├── vim-survival.md          # Exit Vim → Be dangerous in 10 min
│   ├── vim-productive.md        # Navigation, editing, text objects
│   ├── vim-config.md            # .vimrc, plugins, mappings
│   └── vscode-tips.md           # Future addition
│
├── containers/
│   ├── README.md
│   ├── docker-essentials.md     # run, ps, exec, logs - daily use
│   ├── docker-recipes.md        # Common scenarios solved
│   ├── docker-cleanup.md        # 🔥 "Reclaim 50GB in 5 commands"
│   ├── docker-compose.md        # Multi-container workflows
│   └── kubernetes-basics.md     # Future addition
│
├── networking/
│   ├── README.md
│   ├── curl-essentials.md       # HTTP requests, APIs
│   ├── ssh-mastery.md           # Keys, config, tunnels, jumps
│   ├── debugging.md             # 🔥 Flowchart: "Why can't I connect?"
│   └── dns-ports.md             # dig, nslookup, netstat, lsof
│
├── package-managers/
│   ├── README.md
│   ├── npm-essentials.md        # Daily npm commands
│   ├── yarn-pnpm.md             # Alternatives comparison
│   ├── homebrew.md              # macOS package management
│   └── troubleshooting.md       # Common errors, cache issues
│
├── scripts/                     # 🆕 NEW SECTION
│   ├── README.md                # What this section is about
│   ├── auto-update-macos.md     # Moved from package-managers
│   ├── dev-environment.md       # Setup scripts for new machines
│   └── git-hooks.md             # Pre-commit, commit-msg examples
│
└── troubleshooting/             # 🆕 NEW SECTION
    ├── README.md                # How to use this section
    ├── git-errors.md            # Common git error messages decoded
    ├── npm-errors.md            # node_modules nightmares solved
    ├── docker-errors.md         # Container issues
    └── ssh-errors.md            # Permission denied, connection refused
```

---

## Section-by-Section Transformation

### 1. Terminal Section

#### Current State (5 files, ~3500 lines)
| File | Lines | Issue |
|------|-------|-------|
| commands.md | 718 | Overlaps with one-liners |
| bash-one-liners.md | 1228 | Way too exhaustive |
| zsh-tips.md | 566 | Good but needs pruning |
| shell-scripting.md | 1216 | Too comprehensive |
| keyboard-shortcuts.md | 430 | Keep most of this |

#### Transformation Plan

| Current File | → New Location | Action |
|--------------|----------------|--------|
| commands.md | essentials.md | Extract top 50 commands only |
| bash-one-liners.md | one-liners.md + recipes/* | Split: 30 one-liners + categorized recipes |
| zsh-tips.md | productivity/shell-setup.md | Keep Oh-My-Zsh config, best aliases |
| shell-scripting.md | scripting.md | Cut to essentials (loops, conditionals, functions) |
| keyboard-shortcuts.md | productivity/shell-setup.md | Merge into productivity |

#### Unique Value-Adds
- **Problem-first recipes**: "I need to find all large files" not "find command options"
- **One-liners with context**: Each one-liner explains WHEN you'd use it
- **Curated aliases file**: Downloadable .aliases file

#### Target: ~800 lines total (down from 3500)

---

### 2. Git Section

#### Current State (2 files, ~1300 lines)
| File | Lines | Quality |
|------|-------|---------|
| commands.md | ~625 | Good but needs organization |
| concepts.md | 675 | Excellent deep content |

#### Transformation Plan

| Current Content | → New Location | Action |
|-----------------|----------------|--------|
| Basic commands | daily-workflow.md | Extract add/commit/push/pull |
| Branch commands | branching.md | Consolidate branch/merge/rebase |
| Reset/revert/checkout | oops-recovery.md | 🔥 Frame as problem-solving |
| Log/diff/blame | history.md | Keep best examples |
| Cherry-pick, stash | team-workflows.md | Add PR workflow |
| Concepts: 3 trees, reflog | oops-recovery.md | Integrate theory with practice |
| Config, aliases | config.md | Best .gitconfig |

#### Unique Value-Adds
- **oops-recovery.md**: THE differentiator
  - "I committed to wrong branch"
  - "I pushed secrets"
  - "I need to undo last 3 commits"
  - "My rebase went wrong"
- **Visual diagrams**: ASCII art showing git states

#### Target: ~1000 lines total (organized better)

---

### 3. Regex Section

#### Current State (1 file, 709 lines)
| File | Lines | Quality |
|------|-------|---------|
| patterns.md | 709 | Comprehensive but overwhelming |

#### Transformation Plan

| Current Content | → New Location | Action |
|-----------------|----------------|--------|
| Character classes, quantifiers | basics.md | Core reference table |
| Email, URL, phone patterns | recipes.md | With TEST STRINGS |
| Lookahead, groups | basics.md | Advanced section |
| Language-specific | by-language.md | JS/Python/grep differences |

#### Unique Value-Adds
- **Test strings with each pattern**: "Matches: X, Y | Doesn't match: Z"
- **Copy-paste ready**: No modification needed
- **Common mistakes**: "Why your regex isn't working"

#### Target: ~500 lines total (more useful)

---

### 4. Editors Section (Vim)

#### Current State (1 file, 838 lines)
| File | Lines | Quality |
|------|-------|---------|
| vim.md | 838 | Thorough but no learning path |

#### Transformation Plan

| Current Content | → New Location | Action |
|-----------------|----------------|--------|
| Exit, modes, basic nav | vim-survival.md | 🔥 "Survive your first week" |
| Advanced navigation | vim-productive.md | After survival mode |
| Text objects, macros | vim-productive.md | Power user section |
| .vimrc examples | vim-config.md | Minimal + recommended |

#### Unique Value-Adds
- **vim-survival.md**: "Exit Vim" to "Be dangerous" in 10 minutes
- **Progressive learning**: Clear path from scared → productive
- **Minimal .vimrc**: One that actually makes sense

#### Target: ~600 lines total (better organized)

---

### 5. Containers Section (Docker)

#### Current State (1 file, 753 lines)
| File | Lines | Quality |
|------|-------|---------|
| docker.md | 753 | Good commands, needs recipes |

#### Transformation Plan

| Current Content | → New Location | Action |
|-----------------|----------------|--------|
| run, ps, stop, rm | docker-essentials.md | Daily commands |
| exec, logs, inspect | docker-essentials.md | Debugging commands |
| cp, stats | docker-recipes.md | With use cases |
| (new) | docker-cleanup.md | 🔥 Reclaim disk space |
| (new) | docker-compose.md | Multi-container |

#### Unique Value-Adds
- **docker-cleanup.md**:
  - "You will run out of disk space" warning
  - Aggressive cleanup one-liner
  - What's safe to delete
- **Common scenarios**: Debug container, extract files, check resources

#### Target: ~700 lines total (plus new files)

---

### 6. Networking Section

#### Current State (1 file, 795 lines)
| File | Lines | Quality |
|------|-------|---------|
| commands.md | 795 | Comprehensive but flat |

#### Transformation Plan

| Current Content | → New Location | Action |
|-----------------|----------------|--------|
| curl examples | curl-essentials.md | API testing focus |
| SSH commands | ssh-mastery.md | Keys, tunnels, config |
| ping, traceroute | debugging.md | Part of flowchart |
| dig, nslookup | dns-ports.md | DNS troubleshooting |
| netstat, lsof | dns-ports.md | Port management |

#### Unique Value-Adds
- **debugging.md**: Decision flowchart
  ```
  Can't connect?
  → Is it DNS? (dig)
  → Is port open? (nc)
  → Is service running? (curl localhost)
  → Is firewall blocking? (iptables)
  ```
- **ssh-mastery.md**: .ssh/config examples for jump hosts

#### Target: ~600 lines total (more actionable)

---

### 7. Package Managers Section

#### Current State (1 file, 1323 lines)
| File | Lines | Quality |
|------|-------|---------|
| auto-update-macos.md | 1323 | 🔥 Excellent real-world guide |

#### Transformation Plan

| Current Content | → New Location | Action |
|-----------------|----------------|--------|
| auto-update-macos.md | scripts/auto-update-macos.md | Move to scripts section |
| (new) | npm-essentials.md | Daily npm commands |
| (new) | yarn-pnpm.md | When to use which |
| (new) | homebrew.md | macOS essentials |
| (new) | troubleshooting.md | Cache, permissions, conflicts |

#### Unique Value-Adds
- **npm-essentials.md**: Only what you use daily
- **Troubleshooting**: "node_modules" nightmare solutions
- **Comparison table**: npm vs yarn vs pnpm

#### Target: ~400 lines (excluding auto-update moved to scripts)

---

### 8. Scripts Section (NEW)

#### Purpose
Real-world automation scripts that solve actual problems.

#### Initial Content
| File | Description |
|------|-------------|
| auto-update-macos.md | Moved from package-managers |
| dev-environment.md | New machine setup script |
| git-hooks.md | Pre-commit examples |

#### Unique Value-Adds
- **Actual scripts you can use**: Not tutorials, working code
- **Problem context**: Why this script exists

---

### 9. Troubleshooting Section (NEW)

#### Purpose
"I got this error" → "Here's the fix"

#### Initial Content
| File | Common Errors |
|------|---------------|
| git-errors.md | "detached HEAD", "merge conflicts", "rejected push" |
| npm-errors.md | EACCES, ENOENT, peer dependencies |
| docker-errors.md | "port already in use", "no space left" |
| ssh-errors.md | "permission denied", "connection refused" |

#### Format
```markdown
## Error: Permission denied (publickey)

### What it means
SSH server rejected your key authentication.

### Quick fix
```bash
ssh-add ~/.ssh/id_ed25519
```

### If that doesn't work
1. Check if key exists: `ls -la ~/.ssh/`
2. Check SSH agent: `ssh-add -l`
3. Verify key on server: ...
```

---

## Implementation Order

### Phase 1: Terminal (Template)
Restructure terminal section first as the template for all others.

1. Create terminal/README.md with section overview
2. Create terminal/essentials.md (top 50 commands)
3. Create terminal/recipes/ directory with 4 recipe files
4. Create terminal/productivity/ with shell setup and aliases
5. Condense scripting.md to essentials only
6. Curate one-liners.md to top 30
7. Delete old files after verification

### Phase 2: Git
Apply same pattern, create oops-recovery.md as unique value.

### Phase 3: Remaining Sections
- Regex (simplify + add test strings)
- Vim (create survival guide)
- Docker (add cleanup guide)
- Networking (add debugging flowchart)
- Package Managers (add essentials)

### Phase 4: New Sections
- Create scripts/ section
- Create troubleshooting/ section
- Move auto-update-macos.md

### Phase 5: Finalize
- Update main README.md with new structure
- Delete RESTRUCTURE-PLAN.md
- Final review pass

---

## Content Guidelines (All Sections)

### What to Keep
- Commands used weekly or more
- Problem-solving recipes
- Gotchas that waste hours
- Copy-paste ready examples

### What to Cut
- Rarely-used options
- Exhaustive flag lists
- Theoretical explanations (unless essential)
- Duplicate content

### Format Standard
```markdown
## Command/Recipe Name

**When to use**: One sentence explaining the scenario.

```bash
# The command with realistic example
actual-command --with realistic-values
```

> **Tip**: Optional insight that saves time.
```

---

## Success Metrics

After restructure, dev-toolkit should:

1. **Be bookmarkable**: Devs return to it regularly
2. **Solve problems fast**: Find answer in <30 seconds
3. **Be unique**: Content competitors don't have
4. **Be maintainable**: Easy to update and extend

---

## Ready to Begin

Next step: Restructure Terminal section as the template.
