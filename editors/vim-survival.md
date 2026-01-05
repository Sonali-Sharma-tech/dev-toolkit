# Vim Survival Guide

> Opened Vim by accident? Start here.

You're stuck in Vim. No shame. This guide gets you out - and eventually, in control.

## Table of Contents
- [GET OUT (Emergency Exits)](#get-out-emergency-exits)
- [I Need to Type Something](#i-need-to-type-something)
- [I Made Changes I Want to Keep](#i-made-changes-i-want-to-keep)
- [I Messed Up and Want to Undo](#i-messed-up-and-want-to-undo)
- [Basic Survival Kit](#basic-survival-kit)
- [The 5-Minute Crash Course](#the-5-minute-crash-course)
- [Cheat Sheet to Tape on Your Monitor](#cheat-sheet-to-tape-on-your-monitor)

---

## GET OUT (Emergency Exits)

### Situation: I just want to leave

```vim
:q
```
Press `Esc` first (maybe a few times), then type `:q` and press Enter.

### Situation: It says "No write since last change"

You made changes. Decide:

```vim
:q!         " Quit WITHOUT saving (lose changes)
:wq         " Save and quit
```

### Situation: I can't type `:` at all

You're in Insert mode. Press `Esc` first, then try `:q` again.

### Situation: Nothing works, I'm panicking

Press `Esc` three times, then type:
```vim
:qa!
```
This quits ALL files without saving. Nuclear option.

---

## I Need to Type Something

Vim starts in "Normal mode" - keys are commands, not letters.

### To start typing:

Press `i` (for "insert")

Now you can type normally. You'll see `-- INSERT --` at the bottom.

### To stop typing:

Press `Esc`

Now you're back in Normal mode.

### That's the core concept:

```
Esc → Normal mode (commands)
i   → Insert mode (typing)
```

---

## I Made Changes I Want to Keep

### Save and keep editing:

```vim
:w
```

### Save and quit:

```vim
:wq
```
Or in Normal mode, press: `ZZ` (capital Z, twice)

### Save to a different file:

```vim
:w newfilename.txt
```

### Save when you don't have permission:

```vim
:w !sudo tee %
```
Type your password when prompted, then `:q!` to exit.

---

## I Messed Up and Want to Undo

### Undo last change:

```vim
u
```
Press `u` in Normal mode (press `Esc` first if typing).

### Undo multiple changes:

Keep pressing `u`. Each press undoes one change.

### Redo (undo the undo):

```vim
Ctrl+r
```

### Undo everything on the current line:

```vim
U
```

---

## Basic Survival Kit

### Moving Around

```
       k
       ↑
   h ← + → l
       ↓
       j
```

Or just use **arrow keys** - they work too.

### Other Movement

| Key | Where it goes |
|-----|---------------|
| `gg` | Top of file |
| `G` | Bottom of file |
| `0` | Start of line |
| `$` | End of line |
| `w` | Next word |
| `b` | Previous word |

### Deleting Things

| Key | What it deletes |
|-----|-----------------|
| `x` | Character under cursor |
| `dd` | Entire line |
| `dw` | Word |
| `d$` or `D` | Rest of line |

### Copy and Paste

| Key | Action |
|-----|--------|
| `yy` | Copy (yank) line |
| `p` | Paste below |
| `P` | Paste above |

---

## The 5-Minute Crash Course

### The Big Idea

Vim has **modes**:

| Mode | Purpose | How to enter |
|------|---------|--------------|
| Normal | Navigate, run commands | `Esc` |
| Insert | Type text | `i`, `a`, `o` |
| Visual | Select text | `v` |
| Command | Run commands with `:` | `:` |

### The Grammar of Vim

Commands follow a pattern: **verb + object**

| Verb | Meaning |
|------|---------|
| `d` | delete |
| `c` | change (delete and start typing) |
| `y` | yank (copy) |
| `v` | visual select |

| Object | Meaning |
|--------|---------|
| `w` | word |
| `$` | to end of line |
| `}` | to end of paragraph |
| `iw` | inner word |
| `i"` | inside quotes |

### Examples

```
dw      delete word
d$      delete to end of line
ciw     change inner word (delete word, start typing)
yi"     yank inside quotes
```

### Entering Insert Mode

| Key | Where cursor goes |
|-----|-------------------|
| `i` | Before cursor |
| `a` | After cursor |
| `I` | Start of line |
| `A` | End of line |
| `o` | New line below |
| `O` | New line above |

### Quick Workflow

```
1. Open file:     vim filename.txt
2. Press i        (enter Insert mode)
3. Type stuff
4. Press Esc      (back to Normal mode)
5. Type :wq       (save and quit)
```

---

## Cheat Sheet to Tape on Your Monitor

```
┌──────────────────────────────────────────┐
│              VIM SURVIVAL                │
├──────────────────────────────────────────┤
│                                          │
│  GET OUT                                 │
│  ─────────────────────────────────────   │
│  Esc Esc Esc    → back to normal         │
│  :q!            → quit (lose changes)    │
│  :wq            → save and quit          │
│                                          │
│  TYPE TEXT                               │
│  ─────────────────────────────────────   │
│  i              → start typing           │
│  Esc            → stop typing            │
│  o              → new line, start typing │
│                                          │
│  FIX MISTAKES                            │
│  ─────────────────────────────────────   │
│  u              → undo                   │
│  Ctrl+r         → redo                   │
│                                          │
│  MOVE AROUND                             │
│  ─────────────────────────────────────   │
│  h j k l        → ← ↓ ↑ →                │
│  gg             → top of file            │
│  G              → bottom of file         │
│  w / b          → next / prev word       │
│                                          │
│  DELETE                                  │
│  ─────────────────────────────────────   │
│  x              → delete character       │
│  dd             → delete line            │
│  dw             → delete word            │
│                                          │
│  COPY/PASTE                              │
│  ─────────────────────────────────────   │
│  yy             → copy line              │
│  p              → paste                  │
│                                          │
│  SEARCH                                  │
│  ─────────────────────────────────────   │
│  /word          → search forward         │
│  n              → next match             │
│                                          │
└──────────────────────────────────────────┘
```

---

## Common Situations

### Situation: I need to edit a config file

```bash
# Open the file
vim /etc/nginx/nginx.conf

# In vim:
i                    # Start insert mode
# Make your edits
Esc                  # Back to normal mode
:wq                  # Save and quit

# If permission denied:
:w !sudo tee %       # Save with sudo
:q!                  # Quit
```

### Situation: Git opened Vim for a commit message

```bash
# Git opened vim for commit message:
i                    # Start typing
# Type your commit message
Esc                  # Stop typing
:wq                  # Save and close
```

To avoid this in the future:
```bash
git config --global core.editor "nano"    # or "code --wait"
```

### Situation: I need to find and replace text

```vim
:%s/old/new/g
```
- `%` = entire file
- `s` = substitute
- `old` = what to find
- `new` = replacement
- `g` = all occurrences (not just first per line)

### Situation: I need to go to a specific line

```vim
:42         " Go to line 42
42G         " Also go to line 42
```

### Situation: I accidentally deleted something

```vim
u           " Undo
u           " Undo again
u           " Keep going...
```

### Situation: I want to select multiple lines

```vim
V           " Start line selection (Visual Line mode)
j j j       " Move down to select more lines
d           " Delete them
# or
y           " Copy them
```

---

## Why Even Learn This?

You'll encounter Vim when:
- Server has no other editor
- Git opens it for commit/merge messages
- Editing crontabs (`crontab -e`)
- SSH into minimal environments
- Quick config file edits

You don't need to master Vim. Just survive it.

---

## Next Steps

If you want to be productive (not just survive):

- [Vim Productivity](vim-productive.md) - Commands that save time
- [Vim Configuration](vim-config.md) - Make it feel like home

---

*"To exit Vim, you simply need to..."* - Famous last words
