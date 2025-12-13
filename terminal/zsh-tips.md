# Zsh Tips & Configuration

Zsh-specific features, Oh My Zsh setup, plugins, themes, and productivity tips.

## Table of Contents
- [Why Zsh](#why-zsh)
- [Installation](#installation)
- [Oh My Zsh](#oh-my-zsh)
- [Essential Plugins](#essential-plugins)
- [Themes](#themes)
- [Zsh-Specific Features](#zsh-specific-features)
- [Configuration (.zshrc)](#configuration-zshrc)
- [Prompt Customization](#prompt-customization)
- [Autocompletion](#autocompletion)
- [History Configuration](#history-configuration)
- [Useful Zsh Options](#useful-zsh-options)

---

## Why Zsh

Zsh offers features that Bash doesn't have out of the box:
- Better autocompletion (tab complete with previews)
- Spelling correction
- Shared history across sessions
- Better globbing patterns
- Plugin ecosystem (Oh My Zsh)
- Themes and customization

---

## Installation

### macOS
```bash
# Zsh is default shell since macOS Catalina
# Check current shell
echo $SHELL

# If not zsh, change it
chsh -s /bin/zsh
```

### Linux
```bash
# Ubuntu/Debian
sudo apt install zsh

# CentOS/RHEL
sudo dnf install zsh

# Set as default shell
chsh -s $(which zsh)
```

### Verify installation
```bash
zsh --version
```

---

## Oh My Zsh

Oh My Zsh is a framework for managing Zsh configuration with themes and plugins.

### Install Oh My Zsh
```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### Update Oh My Zsh
```bash
omz update
```

### Uninstall
```bash
uninstall_oh_my_zsh
```

### Directory structure
```
~/.oh-my-zsh/
├── custom/           # Your customizations
│   ├── plugins/      # Custom plugins
│   └── themes/       # Custom themes
├── plugins/          # Built-in plugins
├── themes/           # Built-in themes
└── oh-my-zsh.sh      # Main script
```

---

## Essential Plugins

Edit `~/.zshrc` and add plugins:
```bash
plugins=(
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
    z
    docker
    kubectl
    npm
    node
    python
    brew
)
```

### Install third-party plugins

#### zsh-autosuggestions (fish-like suggestions)
```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```
Shows suggestions as you type based on history. Press → to accept.

#### zsh-syntax-highlighting (command highlighting)
```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```
Valid commands show green, invalid show red.

#### fzf (fuzzy finder)
```bash
brew install fzf
$(brew --prefix)/opt/fzf/install    # Install shell integrations
```
Press `Ctrl+R` for fuzzy history search, `Ctrl+T` for file search.

#### z (directory jumping)
```bash
# Built into Oh My Zsh, just add to plugins
plugins=(... z ...)
```
Jump to frequently used directories: `z projects` → goes to `/path/to/your/projects`

#### fast-syntax-highlighting (faster alternative)
```bash
git clone https://github.com/zdharma-continuum/fast-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/fast-syntax-highlighting
```

### Plugin descriptions

| Plugin | What it does |
|--------|--------------|
| git | Git aliases and functions (gst, gco, gp, etc.) |
| z | Jump to directories by frecency |
| docker | Docker autocompletion |
| kubectl | Kubernetes autocompletion + aliases |
| npm | npm autocompletion + aliases |
| node | Node.js version info |
| brew | Homebrew autocompletion |
| sudo | Press ESC twice to add sudo |
| copypath | Copy current path to clipboard |
| copyfile | Copy file content to clipboard |
| web-search | Search from terminal: `google query` |
| extract | Universal extract command for any archive |

---

## Themes

### Set theme in ~/.zshrc
```bash
ZSH_THEME="robbyrussell"    # Default
ZSH_THEME="agnoster"        # Popular, needs powerline font
ZSH_THEME="powerlevel10k/powerlevel10k"  # Most customizable
```

### Popular themes

#### Powerlevel10k (recommended)
```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k

# Set in .zshrc
ZSH_THEME="powerlevel10k/powerlevel10k"

# Run configuration wizard
p10k configure
```
Features: Fast, highly customizable, instant prompt, git status, command timing.

#### Starship (cross-shell)
```bash
brew install starship

# Add to end of ~/.zshrc
eval "$(starship init zsh)"

# Configure
mkdir -p ~/.config && touch ~/.config/starship.toml
```

### Install Nerd Fonts (for icons)
```bash
brew install --cask font-meslo-lg-nerd-font
brew install --cask font-fira-code-nerd-font
brew install --cask font-jetbrains-mono-nerd-font
```
Then set your terminal font to one of these.

---

## Zsh-Specific Features

### Globbing (advanced pattern matching)
```bash
# Recursive globbing
ls **/*.js                  # All .js files in all subdirectories

# Negation
ls ^*.txt                   # Everything except .txt files (needs extendedglob)

# Numeric ranges
ls file{1..5}.txt          # file1.txt through file5.txt

# Qualifiers (requires setopt extendedglob)
ls *(.)                    # Only files
ls *(/)                    # Only directories
ls *(@)                    # Only symlinks
ls *(m-7)                  # Modified in last 7 days
ls *(Lk+100)               # Larger than 100KB
ls *(om[1,5])              # 5 most recently modified
```

### Enable extended globbing
```bash
setopt extendedglob
```

### Parameter expansion
```bash
name="hello.world.txt"
echo ${name:r}              # hello.world (remove extension)
echo ${name:e}              # txt (get extension)
echo ${name:h}              # . (head/directory part)
echo ${name:t}              # hello.world.txt (tail/filename part)
echo ${name:u}              # HELLO.WORLD.TXT (uppercase)
echo ${name:l}              # hello.world.txt (lowercase)
```

### Suffix aliases (open files by extension)
```bash
alias -s py=python3         # ./script.py runs with python3
alias -s js=node            # ./file.js runs with node
alias -s json=code          # file.json opens in VS Code
alias -s md=code            # README.md opens in VS Code
alias -s txt=cat            # file.txt displays with cat
alias -s log=tail           # file.log tails the file
```

### Global aliases (expand anywhere in command)
```bash
alias -g G='| grep'         # ls G pattern → ls | grep pattern
alias -g L='| less'         # cat file G | less
alias -g H='| head'         # ls -la H
alias -g T='| tail'         # cat log T
alias -g C='| wc -l'        # find . C → count lines
alias -g J='| jq .'         # curl api J → pretty JSON
alias -g NE='2>/dev/null'   # command NE → suppress errors
alias -g NUL='>/dev/null 2>&1'  # command NUL → suppress all output
```

### Directory shortcuts
```bash
# Named directories
hash -d proj=~/Documents/Projects
hash -d work=~/workspace
hash -d dl=~/Downloads

# Now use as:
cd ~proj                    # Goes to ~/Documents/Projects
ls ~work                    # Lists ~/workspace
```

### Spelling correction
```bash
setopt correct              # Correct commands
setopt correctall           # Correct all arguments

# Example:
gti status                  # Suggests: git status [nyae]?
```

---

## Configuration (.zshrc)

### Sample .zshrc structure
```bash
# Path to oh-my-zsh
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

# Load Oh My Zsh
source $ZSH/oh-my-zsh.sh

# ============ User Configuration ============

# Environment variables
export EDITOR="code --wait"
export PATH="$HOME/bin:$PATH"

# Aliases
alias ll="ls -alh"
alias gs="git status"
alias gp="git pull"

# Functions
mkcd() { mkdir -p "$1" && cd "$1"; }

# Load additional configs
[[ -f ~/.zsh_aliases ]] && source ~/.zsh_aliases
[[ -f ~/.zsh_functions ]] && source ~/.zsh_functions
[[ -f ~/.zsh_secrets ]] && source ~/.zsh_secrets
```

### Reload configuration
```bash
source ~/.zshrc
# Or
exec zsh
# Or with Oh My Zsh
omz reload
```

---

## Prompt Customization

### Basic PROMPT variable
```bash
# Simple prompt
PROMPT='%n@%m %~ $ '         # username@hostname ~/directory $

# With colors
PROMPT='%F{green}%n%f@%F{blue}%m%f %F{yellow}%~%f $ '

# With git info (requires vcs_info)
autoload -Uz vcs_info
precmd() { vcs_info }
zstyle ':vcs_info:git:*' formats ' (%b)'
PROMPT='%F{cyan}%~%f${vcs_info_msg_0_} $ '
```

### Prompt escape codes
| Code | Meaning |
|------|---------|
| %n | Username |
| %m | Hostname (short) |
| %M | Hostname (full) |
| %~ | Current directory (~ for home) |
| %d | Full current directory |
| %T | Time (HH:MM) |
| %* | Time (HH:MM:SS) |
| %D | Date (YY-MM-DD) |
| %? | Exit status of last command |
| %# | # for root, % for user |

### Colors
```bash
%F{color}text%f              # Foreground color
%K{color}text%k              # Background color

# Colors: black, red, green, yellow, blue, magenta, cyan, white
# Or use numbers 0-255
```

---

## Autocompletion

### Enable advanced completion
```bash
autoload -Uz compinit && compinit
```

### Completion styling
```bash
# Case-insensitive completion
zstyle ':completion:*' matcher-list 'm:{a-z}={A-Z}'

# Partial completion (cd /u/l/b → /usr/local/bin)
zstyle ':completion:*' matcher-list 'r:|[._-]=* r:|=*' 'l:|=* r:|=*'

# Menu selection (arrow keys)
zstyle ':completion:*' menu select

# Colored completions
zstyle ':completion:*' list-colors ${(s.:.)LS_COLORS}

# Group completions
zstyle ':completion:*' group-name ''
zstyle ':completion:*:descriptions' format '%F{yellow}-- %d --%f'

# Cache completions (faster)
zstyle ':completion:*' use-cache on
zstyle ':completion:*' cache-path ~/.zsh/cache
```

### Custom completions
```bash
# Complete hostnames from known_hosts
zstyle ':completion:*:ssh:*' hosts $(grep -oE '^[^ ,]+' ~/.ssh/known_hosts 2>/dev/null)

# Complete kill with process names
zstyle ':completion:*:*:kill:*:processes' list-colors '=(#b) #([0-9]#)*=0=01;31'
```

---

## History Configuration

### History settings
```bash
HISTFILE=~/.zsh_history     # History file location
HISTSIZE=50000              # Lines to keep in memory
SAVEHIST=50000              # Lines to save to file

# Share history between sessions
setopt share_history

# Don't store duplicates
setopt hist_ignore_all_dups

# Don't store commands starting with space
setopt hist_ignore_space

# Append to history (don't overwrite)
setopt append_history

# Add timestamp to history
setopt extended_history

# Write history immediately (not on exit)
setopt inc_append_history

# Remove extra blanks from commands
setopt hist_reduce_blanks
```

### History search
```bash
# Search history with arrow keys
bindkey '^[[A' history-search-backward
bindkey '^[[B' history-search-forward

# Type "git" then press Up → only git commands
```

### History commands
```bash
history                     # Show history
history -10                 # Last 10 commands
fc -l 1                     # Full history with numbers
!123                        # Run command #123
!!                          # Repeat last command
!git                        # Last command starting with 'git'
!?search?                   # Last command containing 'search'
```

---

## Useful Zsh Options

Add to `~/.zshrc`:

```bash
# Navigation
setopt auto_cd              # cd by typing directory name
setopt auto_pushd           # Push directories to stack
setopt pushd_ignore_dups    # Don't push duplicates
setopt pushd_silent         # Don't print directory stack

# Completion
setopt always_to_end        # Move cursor to end after completion
setopt auto_menu            # Show completion menu on tab
setopt complete_in_word     # Complete from cursor position
setopt no_list_beep         # Don't beep on completion

# Globbing
setopt extended_glob        # Extended pattern matching
setopt glob_dots            # Include dotfiles in globbing

# Correction
setopt correct              # Correct command spelling
setopt correct_all          # Correct all arguments

# History
setopt share_history        # Share between sessions
setopt hist_ignore_all_dups # No duplicates
setopt hist_ignore_space    # Ignore commands with leading space

# Safety
setopt no_clobber           # Don't overwrite files with >
setopt rm_star_wait         # Wait before rm * confirmation

# Other
setopt interactive_comments # Allow comments in interactive shell
setopt long_list_jobs       # List jobs in long format
setopt notify               # Report background job status immediately
```

### Check current options
```bash
setopt                      # List enabled options
unsetopt                    # List disabled options
```

---

## Quick Reference

### Key bindings
| Binding | Action |
|---------|--------|
| Tab | Autocomplete |
| Ctrl+R | Reverse history search |
| Ctrl+A | Beginning of line |
| Ctrl+E | End of line |
| Ctrl+U | Delete to beginning |
| Ctrl+K | Delete to end |
| Ctrl+W | Delete word backward |
| Alt+. | Insert last argument |
| ESC ESC | Add sudo (with plugin) |

### Directory navigation
```bash
cd -                        # Previous directory
cd -2                       # 2 directories ago
dirs -v                     # Show directory stack
~-                          # Previous directory
~+2                         # 2nd in stack
```

### Oh My Zsh shortcuts
```bash
omz update                  # Update Oh My Zsh
omz reload                  # Reload config
omz plugin list             # List plugins
omz theme list              # List themes
```

---

*Pro tip: Start with a minimal config, add plugins/features gradually as you understand them.*
