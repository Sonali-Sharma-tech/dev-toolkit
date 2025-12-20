# Aliases

> Shortcuts that actually save time. Add to `~/.zshrc` or `~/.aliases`.

---

## Navigation

```bash
alias ..="cd .."
alias ...="cd ../.."
alias ....="cd ../../.."
alias ~="cd ~"
alias -- -="cd -"             # Previous directory

# Project shortcuts (customize these)
alias proj="cd ~/Projects"
alias work="cd ~/workspace"
alias dl="cd ~/Downloads"
```

---

## Listing

```bash
alias ls="ls --color=auto"    # Linux
alias ls="ls -G"              # macOS

alias l="ls -lah"             # Long, all, human-readable
alias ll="ls -lh"             # Long, human-readable
alias la="ls -A"              # All except . and ..
alias lt="ls -lahtr"          # By time, oldest first
```

---

## Git (Essential)

```bash
alias g="git"
alias gs="git status"
alias gp="git pull"
alias gpu="git push"
alias gco="git checkout"
alias gcb="git checkout -b"
alias gc="git commit -m"
alias gca="git commit --amend --no-edit"
alias gd="git diff"
alias gds="git diff --staged"
alias gl="git log --oneline -10"
alias glog="git log --graph --oneline --decorate"
alias gb="git branch"
alias gba="git branch -a"
alias gst="git stash"
alias gstp="git stash pop"

# Clean up merged branches
alias gbclean='git branch --merged | grep -v "\*\|main\|master" | xargs -n 1 git branch -d'
```

---

## Docker

```bash
alias d="docker"
alias dc="docker compose"
alias dps="docker ps"
alias dpsa="docker ps -a"
alias di="docker images"
alias dex="docker exec -it"
alias dlogs="docker logs -f"

# Cleanup
alias dprune="docker system prune -af"
alias dstop='docker stop $(docker ps -q)'
alias drm='docker rm $(docker ps -aq)'
alias drmi='docker rmi $(docker images -q)'
```

---

## npm/Node

```bash
alias ni="npm install"
alias nid="npm install --save-dev"
alias nr="npm run"
alias nrd="npm run dev"
alias nrb="npm run build"
alias nrt="npm run test"
alias ns="npm start"

# Clean reinstall
alias nuke="rm -rf node_modules package-lock.json && npm install"
```

---

## Python

```bash
alias py="python3"
alias pip="pip3"
alias venv="python3 -m venv venv"
alias activate="source venv/bin/activate"

# Django
alias pm="python manage.py"
alias pmr="python manage.py runserver"
alias pmm="python manage.py migrate"
```

---

## System

```bash
alias c="clear"
alias reload="exec $SHELL"
alias path='echo $PATH | tr ":" "\n" | nl'
alias h="history | grep"

# Editors
alias v="vim"
alias code.="code ."

# Quick edits
alias zshrc="$EDITOR ~/.zshrc"
alias bashrc="$EDITOR ~/.bashrc"
alias hosts="sudo $EDITOR /etc/hosts"
```

---

## Network

```bash
alias ip="curl -s ifconfig.me"
alias localip="ipconfig getifaddr en0"   # macOS WiFi
alias ports="lsof -i -P -n | grep LISTEN"
alias flushdns="sudo dscacheutil -flushcache && sudo killall -HUP mDNSResponder"  # macOS
```

---

## Safety Nets

```bash
# Confirm before overwriting
alias cp="cp -i"
alias mv="mv -i"
alias rm="rm -i"

# Don't delete root
alias rm="rm --preserve-root"    # Linux
```

---

## Quick Utilities

```bash
# Weather
alias weather="curl wttr.in"

# Quick HTTP server
alias serve="python3 -m http.server 8000"

# Open current directory
alias o="open ."                 # macOS
alias o="xdg-open ."             # Linux

# Clipboard
alias pbcopy="xclip -selection clipboard"    # Linux
alias pbpaste="xclip -selection clipboard -o"
```

---

## Functions

More powerful than aliases - use for anything needing arguments.

```bash
# Create directory and cd into it
mkcd() { mkdir -p "$1" && cd "$1"; }

# Extract any archive
extract() {
  case "$1" in
    *.tar.gz|*.tgz)  tar xzf "$1" ;;
    *.tar.bz2|*.tbz) tar xjf "$1" ;;
    *.tar.xz)        tar xJf "$1" ;;
    *.tar)           tar xf "$1" ;;
    *.zip)           unzip "$1" ;;
    *.gz)            gunzip "$1" ;;
    *.rar)           unrar x "$1" ;;
    *.7z)            7z x "$1" ;;
    *)               echo "Unknown format: $1" ;;
  esac
}

# Kill process on port
killport() { lsof -ti:$1 | xargs kill -9; }

# Quick note
note() { echo "$(date): $*" >> ~/notes.txt; }

# Find and replace in files
replace() {
  find . -type f -name "$1" -exec sed -i '' "s/$2/$3/g" {} \;
}
```

---

## Loading Aliases

### Option 1: In .zshrc directly

Just add the aliases to your `~/.zshrc`.

### Option 2: Separate file (cleaner)

Create `~/.aliases` with your aliases, then in `~/.zshrc`:

```bash
[[ -f ~/.aliases ]] && source ~/.aliases
```

---

## My Essentials (pick your favorites)

The ones I use 50+ times a day:

```bash
alias ll="ls -lah"
alias gs="git status"
alias gp="git pull"
alias gd="git diff"
alias gc="git commit -m"
alias c="clear"
alias ..="cd .."
alias code.="code ."
```

---

## macOS Shortcuts

| Shortcut | Action |
|----------|--------|
| `Cmd + Shift + .` | Toggle hidden files in Finder |

---

*After editing, run `source ~/.zshrc` to apply changes.*
