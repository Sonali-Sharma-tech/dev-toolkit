# Terminal Keyboard Shortcuts

Essential keyboard shortcuts for navigating and editing in the terminal. Works in Bash, Zsh, and most terminal emulators.

## Table of Contents
- [Cursor Movement](#cursor-movement)
- [Text Editing](#text-editing)
- [Cut, Copy, Paste (Kill Ring)](#cut-copy-paste-kill-ring)
- [History Navigation](#history-navigation)
- [Process Control](#process-control)
- [Terminal Emulator Shortcuts](#terminal-emulator-shortcuts)
- [Tmux Shortcuts](#tmux-shortcuts)
- [Screen Shortcuts](#screen-shortcuts)
- [Custom Key Bindings](#custom-key-bindings)

---

## Cursor Movement

| Shortcut | Action |
|----------|--------|
| `Ctrl + A` | Beginning of line |
| `Ctrl + E` | End of line |
| `Ctrl + F` | Forward one character |
| `Ctrl + B` | Back one character |
| `Alt + F` | Forward one word |
| `Alt + B` | Back one word |
| `Ctrl + XX` | Toggle between start and current position |

### Arrows
| Shortcut | Action |
|----------|--------|
| `←` / `→` | Move left/right |
| `Alt + ←` | Back one word (in some terminals) |
| `Alt + →` | Forward one word (in some terminals) |
| `Home` | Beginning of line |
| `End` | End of line |

---

## Text Editing

### Delete characters
| Shortcut | Action |
|----------|--------|
| `Ctrl + D` | Delete character under cursor (or exit if empty) |
| `Ctrl + H` | Delete character before cursor (backspace) |
| `Backspace` | Delete character before cursor |
| `Delete` | Delete character under cursor |

### Delete words
| Shortcut | Action |
|----------|--------|
| `Ctrl + W` | Delete word before cursor |
| `Alt + D` | Delete word after cursor |
| `Alt + Backspace` | Delete word before cursor |

### Delete lines
| Shortcut | Action |
|----------|--------|
| `Ctrl + U` | Delete from cursor to beginning of line |
| `Ctrl + K` | Delete from cursor to end of line |
| `Ctrl + Y` | Paste last deleted text (yank) |

### Transform text
| Shortcut | Action |
|----------|--------|
| `Alt + U` | Uppercase word from cursor |
| `Alt + L` | Lowercase word from cursor |
| `Alt + C` | Capitalize word from cursor |
| `Ctrl + T` | Swap current char with previous |
| `Alt + T` | Swap current word with previous |

### Undo
| Shortcut | Action |
|----------|--------|
| `Ctrl + _` | Undo last edit |
| `Ctrl + X, Ctrl + U` | Undo (alternative) |
| `Alt + R` | Revert line to original |

---

## Cut, Copy, Paste (Kill Ring)

The terminal uses a "kill ring" instead of clipboard:

| Shortcut | Action |
|----------|--------|
| `Ctrl + K` | Kill (cut) to end of line |
| `Ctrl + U` | Kill (cut) to beginning of line |
| `Ctrl + W` | Kill (cut) word before cursor |
| `Alt + D` | Kill (cut) word after cursor |
| `Ctrl + Y` | Yank (paste) last killed text |
| `Alt + Y` | Cycle through kill ring (after Ctrl+Y) |

### System clipboard (macOS Terminal)
| Shortcut | Action |
|----------|--------|
| `Cmd + C` | Copy selected text |
| `Cmd + V` | Paste from clipboard |
| `Cmd + Shift + V` | Paste without formatting |

### iTerm2 specific
| Shortcut | Action |
|----------|--------|
| `Cmd + Shift + H` | Paste history |
| `Cmd + Option + V` | Paste from advanced paste |

---

## History Navigation

### Basic history
| Shortcut | Action |
|----------|--------|
| `↑` / `Ctrl + P` | Previous command |
| `↓` / `Ctrl + N` | Next command |
| `Ctrl + R` | Reverse search history |
| `Ctrl + S` | Forward search history (if enabled) |
| `Ctrl + G` | Cancel search |
| `Alt + .` | Insert last argument of previous command |
| `Alt + <number> Alt + .` | Insert nth argument |

### History search
| Shortcut | Action |
|----------|--------|
| `Ctrl + R` | Start reverse search |
| `Ctrl + R` (again) | Find next match |
| `Enter` | Execute found command |
| `Ctrl + G` / `Ctrl + C` | Cancel search |
| `→` / `Ctrl + E` | Edit found command |

### History expansion
| Command | Action |
|---------|--------|
| `!!` | Last command |
| `!n` | Command number n |
| `!-n` | nth previous command |
| `!string` | Last command starting with "string" |
| `!?string?` | Last command containing "string" |
| `^old^new` | Replace "old" with "new" in last command |
| `!$` | Last argument of previous command |
| `!^` | First argument of previous command |
| `!*` | All arguments of previous command |

### Examples
```bash
# Repeat last command
!!

# Repeat with sudo
sudo !!

# Use last argument
mkdir /path/to/new/dir
cd !$                       # cd to /path/to/new/dir

# Replace and run
echo hello world
^hello^goodbye              # Runs: echo goodbye world
```

---

## Process Control

| Shortcut | Action |
|----------|--------|
| `Ctrl + C` | Kill current process (SIGINT) |
| `Ctrl + Z` | Suspend current process (SIGTSTP) |
| `Ctrl + D` | Exit shell / Send EOF |
| `Ctrl + \` | Kill process (SIGQUIT, with core dump) |
| `Ctrl + S` | Pause output (freeze terminal) |
| `Ctrl + Q` | Resume output (unfreeze terminal) |

### Job control
```bash
# After Ctrl+Z
jobs                        # List suspended jobs
fg                          # Resume in foreground
fg %1                       # Resume job 1 in foreground
bg                          # Resume in background
bg %2                       # Resume job 2 in background
kill %1                     # Kill job 1
```

---

## Terminal Emulator Shortcuts

### macOS Terminal.app
| Shortcut | Action |
|----------|--------|
| `Cmd + T` | New tab |
| `Cmd + N` | New window |
| `Cmd + W` | Close tab |
| `Cmd + Shift + W` | Close window |
| `Cmd + D` | Split pane horizontally |
| `Cmd + Shift + D` | Split pane vertically |
| `Cmd + ]` / `Cmd + [` | Switch panes |
| `Cmd + 1-9` | Switch to tab 1-9 |
| `Cmd + ←` / `Cmd + →` | Previous/next tab |
| `Cmd + +` / `Cmd + -` | Increase/decrease font size |
| `Cmd + 0` | Reset font size |
| `Cmd + K` | Clear screen and scrollback |
| `Cmd + F` | Find |
| `Cmd + G` | Find next |

### iTerm2
| Shortcut | Action |
|----------|--------|
| `Cmd + T` | New tab |
| `Cmd + D` | Split vertically |
| `Cmd + Shift + D` | Split horizontally |
| `Cmd + Option + ←/→` | Switch panes |
| `Cmd + Shift + Enter` | Toggle maximize pane |
| `Cmd + ;` | Autocomplete |
| `Cmd + Shift + H` | Paste history |
| `Cmd + /` | Find cursor |
| `Cmd + Option + E` | Exposé tabs |
| `Cmd + Option + B` | Instant replay |
| `Cmd + Shift + O` | Open quickly (profiles) |
| `Cmd + Enter` | Toggle fullscreen |
| `Cmd + Option + I` | Broadcast input to all panes |

### Warp Terminal
| Shortcut | Action |
|----------|--------|
| `Cmd + D` | Split right |
| `Cmd + Shift + D` | Split down |
| `Ctrl + Shift + ←/→/↑/↓` | Navigate panes |
| `Cmd + P` | Command palette |
| `Cmd + K` | Clear blocks |
| `Cmd + Shift + C` | Copy command output |
| `Ctrl + R` | AI command search |

### VS Code Terminal
| Shortcut | Action |
|----------|--------|
| `Ctrl + ` ` | Toggle terminal |
| `Ctrl + Shift + ` ` | New terminal |
| `Cmd + \` | Split terminal |
| `Cmd + Option + ←/→` | Navigate terminals |
| `Cmd + Shift + [/]` | Switch terminal tabs |
| `Ctrl + Page Up/Down` | Scroll up/down |

### Linux (GNOME Terminal / Konsole)
| Shortcut | Action |
|----------|--------|
| `Ctrl + Shift + T` | New tab |
| `Ctrl + Shift + N` | New window |
| `Ctrl + Shift + W` | Close tab |
| `Ctrl + Shift + Q` | Close window |
| `Ctrl + Shift + C` | Copy |
| `Ctrl + Shift + V` | Paste |
| `Ctrl + Page Up/Down` | Switch tabs |
| `Ctrl + Shift + +/-` | Zoom in/out |

---

## Tmux Shortcuts

Tmux uses a prefix key (default: `Ctrl + B`) before commands.

### Session management
| Shortcut | Action |
|----------|--------|
| `prefix d` | Detach from session |
| `prefix s` | List sessions |
| `prefix $` | Rename session |
| `prefix (` | Previous session |
| `prefix )` | Next session |

### Window management
| Shortcut | Action |
|----------|--------|
| `prefix c` | Create new window |
| `prefix ,` | Rename window |
| `prefix n` | Next window |
| `prefix p` | Previous window |
| `prefix 0-9` | Switch to window 0-9 |
| `prefix w` | List windows |
| `prefix &` | Kill window |

### Pane management
| Shortcut | Action |
|----------|--------|
| `prefix %` | Split vertically |
| `prefix "` | Split horizontally |
| `prefix ←/→/↑/↓` | Navigate panes |
| `prefix o` | Next pane |
| `prefix q` | Show pane numbers |
| `prefix z` | Toggle pane zoom |
| `prefix x` | Kill pane |
| `prefix {` | Move pane left |
| `prefix }` | Move pane right |
| `prefix Space` | Toggle pane layouts |

### Copy mode
| Shortcut | Action |
|----------|--------|
| `prefix [` | Enter copy mode |
| `prefix ]` | Paste buffer |
| `q` | Exit copy mode |
| `Space` | Start selection (in copy mode) |
| `Enter` | Copy selection (in copy mode) |

### Useful tmux commands
```bash
tmux                        # Start new session
tmux new -s name            # New named session
tmux ls                     # List sessions
tmux attach -t name         # Attach to session
tmux kill-session -t name   # Kill session
```

---

## Screen Shortcuts

Screen uses `Ctrl + A` as prefix.

| Shortcut | Action |
|----------|--------|
| `Ctrl+A c` | Create new window |
| `Ctrl+A n` | Next window |
| `Ctrl+A p` | Previous window |
| `Ctrl+A "` | List windows |
| `Ctrl+A 0-9` | Switch to window |
| `Ctrl+A d` | Detach |
| `Ctrl+A k` | Kill window |
| `Ctrl+A S` | Split horizontal |
| `Ctrl+A |` | Split vertical |
| `Ctrl+A Tab` | Switch region |
| `Ctrl+A X` | Remove region |
| `Ctrl+A [` | Enter copy mode |
| `Ctrl+A ]` | Paste |
| `Ctrl+A ?` | Help |

### Screen commands
```bash
screen                      # Start new session
screen -S name              # New named session
screen -ls                  # List sessions
screen -r name              # Reattach to session
screen -X quit              # Kill all sessions
```

---

## Custom Key Bindings

### Bash (~/.inputrc)
```bash
# Create/edit ~/.inputrc

# Case-insensitive completion
set completion-ignore-case on

# Show all matches on first Tab
set show-all-if-ambiguous on

# Append slash to directories
set mark-directories on

# Custom bindings
"\e[A": history-search-backward  # Up arrow searches history
"\e[B": history-search-forward   # Down arrow searches history
"\e[1;5C": forward-word          # Ctrl+Right moves word
"\e[1;5D": backward-word         # Ctrl+Left moves word
```

### Zsh (~/.zshrc)
```bash
# Vim mode
bindkey -v                  # Enable vim mode
bindkey -e                  # Enable emacs mode (default)

# Custom bindings
bindkey '^[[A' history-search-backward  # Up arrow
bindkey '^[[B' history-search-forward   # Down arrow
bindkey '^[[1;5C' forward-word          # Ctrl+Right
bindkey '^[[1;5D' backward-word         # Ctrl+Left

# Find key codes
cat -v                      # Type key to see code
```

### See current bindings
```bash
# Bash
bind -P                     # List all bindings

# Zsh
bindkey                     # List all bindings
bindkey '^R'                # Check specific binding
```

---

## Quick Reference Card

### Most Used (memorize these!)
| Shortcut | Action |
|----------|--------|
| `Ctrl + C` | Cancel/kill |
| `Ctrl + D` | Exit/EOF |
| `Ctrl + Z` | Suspend |
| `Ctrl + L` | Clear screen |
| `Ctrl + R` | Search history |
| `Ctrl + A` | Start of line |
| `Ctrl + E` | End of line |
| `Ctrl + W` | Delete word back |
| `Ctrl + K` | Delete to end |
| `Ctrl + U` | Delete to start |
| `Ctrl + Y` | Paste deleted |
| `Tab` | Autocomplete |
| `↑` / `↓` | History navigation |

### Navigation Muscle Memory
```
Ctrl+A ─────────────────────────────────────── Ctrl+E
       ← Alt+B   Ctrl+B │ Ctrl+F   Alt+F →
                   cursor
```

---

*Pro tip: Practice `Ctrl+R` for history search and `Ctrl+A/E` for line navigation - these alone will save hours.*
