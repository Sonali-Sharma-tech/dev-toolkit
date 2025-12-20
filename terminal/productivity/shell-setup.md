# Shell Setup

> Configure Zsh + Oh-My-Zsh for a productive terminal.

---

## Quick Start (5 minutes)

### 1. Verify Zsh is your shell

```bash
echo $SHELL
# Should show /bin/zsh (default on macOS since Catalina)

# If not, change it
chsh -s /bin/zsh
```

### 2. Install Oh-My-Zsh

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### 3. Install essential plugins

```bash
# Autosuggestions (fish-like suggestions)
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# Syntax highlighting (valid commands = green)
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

### 4. Enable plugins

Edit `~/.zshrc`:

```bash
plugins=(
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
    z
)
```

### 5. Reload

```bash
source ~/.zshrc
```

Done. You now have autosuggestions, syntax highlighting, and directory jumping.

---

## Recommended Plugins

Edit the `plugins=()` line in `~/.zshrc`:

```bash
plugins=(
    git                         # Git aliases (gst, gco, gp, etc.)
    zsh-autosuggestions         # Fish-like suggestions
    zsh-syntax-highlighting     # Valid commands = green
    z                           # Jump to directories (z projects)
    docker                      # Docker autocompletion
    npm                         # npm autocompletion
    brew                        # Homebrew autocompletion
    sudo                        # ESC ESC adds sudo
)
```

### What each plugin does

| Plugin | Feature |
|--------|---------|
| git | Aliases: `gst` = `git status`, `gco` = `git checkout` |
| zsh-autosuggestions | Shows command suggestions as you type (→ to accept) |
| zsh-syntax-highlighting | Green = valid command, Red = invalid |
| z | `z proj` jumps to ~/Documents/Projects |
| sudo | Press ESC twice to prepend sudo |

---

## Theme Setup

### Option A: Powerlevel10k (Recommended)

Best theme - fast, customizable, informative.

```bash
# Install
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k

# Set in ~/.zshrc
ZSH_THEME="powerlevel10k/powerlevel10k"

# Reload and run configuration wizard
source ~/.zshrc
p10k configure
```

### Option B: Starship (Cross-shell)

Works in Zsh, Bash, Fish - good if you switch shells.

```bash
# Install
brew install starship

# Add to end of ~/.zshrc
eval "$(starship init zsh)"
```

### Install Nerd Fonts (for icons)

Themes use special icons. Install a compatible font:

```bash
brew install --cask font-meslo-lg-nerd-font
# Or
brew install --cask font-jetbrains-mono-nerd-font
```

Then set your terminal font to the installed Nerd Font.

---

## Directory Jumping with z

After installing, just use your terminal normally. z learns.

```bash
# After visiting ~/Documents/Projects/webapp a few times:
z webapp              # Jumps there

# Partial matches work
z proj                # Most frecent match

# List matches
z -l proj
```

---

## Fuzzy Finder (fzf)

Superpower for history search and file finding.

```bash
# Install
brew install fzf
$(brew --prefix)/opt/fzf/install

# Usage
Ctrl+R                # Fuzzy search history (way better!)
Ctrl+T                # Fuzzy find files
cd **<Tab>            # Fuzzy directory completion
```

---

## Useful Zsh Options

Add to `~/.zshrc`:

```bash
# Navigation
setopt auto_cd              # Type directory name to cd into it
setopt auto_pushd           # cd pushes to directory stack

# History
setopt share_history        # Share history between terminals
setopt hist_ignore_all_dups # No duplicates in history
setopt hist_ignore_space    # Commands with leading space aren't saved

# Safety
setopt no_clobber           # Prevent > from overwriting (use >| to force)

# Globbing
setopt extended_glob        # Advanced pattern matching
```

---

## Key Bindings

These work out of the box in Zsh:

| Shortcut | Action |
|----------|--------|
| `→` | Accept autosuggestion |
| `Ctrl+R` | Search history (or fuzzy with fzf) |
| `ESC ESC` | Add sudo (with sudo plugin) |
| `Tab` | Autocomplete |
| `Ctrl+A` | Beginning of line |
| `Ctrl+E` | End of line |

### History search with arrows

Add to `~/.zshrc`:

```bash
bindkey '^[[A' history-search-backward
bindkey '^[[B' history-search-forward
```

Now type `git` and press Up - only shows git commands from history.

---

## Sample .zshrc

A clean, working configuration:

```bash
# Path to Oh-My-Zsh
export ZSH="$HOME/.oh-my-zsh"

# Theme
ZSH_THEME="powerlevel10k/powerlevel10k"

# Plugins
plugins=(
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
    z
    docker
    npm
    brew
    sudo
)

# Load Oh-My-Zsh
source $ZSH/oh-my-zsh.sh

# ========== User Configuration ==========

# Editor
export EDITOR="code --wait"

# History
setopt share_history
setopt hist_ignore_all_dups
setopt hist_ignore_space
HISTSIZE=50000
SAVEHIST=50000

# Navigation
setopt auto_cd
setopt auto_pushd

# Key bindings
bindkey '^[[A' history-search-backward
bindkey '^[[B' history-search-forward

# Aliases (or source from separate file)
alias ll="ls -alh"
alias gs="git status"
alias gp="git pull"
alias gc="git commit -m"

# Load additional files if they exist
[[ -f ~/.aliases ]] && source ~/.aliases
[[ -f ~/.zsh_secrets ]] && source ~/.zsh_secrets

# fzf (if installed)
[ -f ~/.fzf.zsh ] && source ~/.fzf.zsh

# Powerlevel10k instant prompt
[[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]] && source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
[[ -f ~/.p10k.zsh ]] && source ~/.p10k.zsh
```

---

## Troubleshooting

### "Command not found: zsh-autosuggestions"

You need to install the plugin, not just add it to the list:

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

### "Slow shell startup"

Profile it:

```bash
time zsh -i -c exit
```

Common causes:
- Too many plugins
- nvm (use fnm instead)
- Unused plugins

### "Icons look weird"

Install a Nerd Font and set it in your terminal preferences.

### "Changes not taking effect"

```bash
source ~/.zshrc
# Or
exec zsh
```

---

*For custom aliases, see [Aliases](aliases.md).*
