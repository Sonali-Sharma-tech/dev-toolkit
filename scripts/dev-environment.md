# Dev Environment Setup Script

> New Mac? Run this script. Get coding in 30 minutes.

## The Problem

Setting up a new development machine takes hours:
- Installing Homebrew
- Installing languages (Node, Python, etc.)
- Installing tools (Git, Docker, etc.)
- Installing apps (VS Code, iTerm, etc.)
- Configuring everything

## The Solution

One script that does it all.

---

## Quick Start

```bash
# Download and run
curl -fsSL https://raw.githubusercontent.com/YOUR_USERNAME/dev-toolkit/main/scripts/setup.sh | bash
```

Or clone and run:
```bash
git clone https://github.com/YOUR_USERNAME/dev-toolkit.git
cd dev-toolkit/scripts
chmod +x setup.sh
./setup.sh
```

---

## The Script

### setup.sh

```bash
#!/bin/bash

# ═══════════════════════════════════════════════════════════════════════════
# DEV ENVIRONMENT SETUP SCRIPT
# ═══════════════════════════════════════════════════════════════════════════
# Run this on a fresh Mac to set up your development environment.
#
# Usage: ./setup.sh
# ═══════════════════════════════════════════════════════════════════════════

set -e  # Exit on error

# Colors for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# ─────────────────────────────────────────────────────────────────────────────
# Helper Functions
# ─────────────────────────────────────────────────────────────────────────────

print_header() {
    echo ""
    echo -e "${BLUE}═══════════════════════════════════════════════════════════════${NC}"
    echo -e "${BLUE}  $1${NC}"
    echo -e "${BLUE}═══════════════════════════════════════════════════════════════${NC}"
    echo ""
}

print_step() {
    echo -e "${GREEN}▶ $1${NC}"
}

print_warning() {
    echo -e "${YELLOW}⚠ $1${NC}"
}

print_error() {
    echo -e "${RED}✗ $1${NC}"
}

print_success() {
    echo -e "${GREEN}✓ $1${NC}"
}

command_exists() {
    command -v "$1" >/dev/null 2>&1
}

# ─────────────────────────────────────────────────────────────────────────────
# 1. XCODE COMMAND LINE TOOLS
# ─────────────────────────────────────────────────────────────────────────────

install_xcode_tools() {
    print_header "Installing Xcode Command Line Tools"

    if xcode-select -p &>/dev/null; then
        print_success "Xcode Command Line Tools already installed"
    else
        print_step "Installing Xcode Command Line Tools..."
        xcode-select --install

        # Wait for installation
        echo "Please complete the Xcode installation dialog, then press Enter..."
        read -r
    fi
}

# ─────────────────────────────────────────────────────────────────────────────
# 2. HOMEBREW
# ─────────────────────────────────────────────────────────────────────────────

install_homebrew() {
    print_header "Installing Homebrew"

    if command_exists brew; then
        print_success "Homebrew already installed"
        print_step "Updating Homebrew..."
        brew update
    else
        print_step "Installing Homebrew..."
        /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

        # Add to PATH for Apple Silicon
        if [[ $(uname -m) == "arm64" ]]; then
            echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
            eval "$(/opt/homebrew/bin/brew shellenv)"
        fi
    fi

    # Verify
    brew doctor || print_warning "Some Homebrew issues detected, but continuing..."
}

# ─────────────────────────────────────────────────────────────────────────────
# 3. CLI TOOLS
# ─────────────────────────────────────────────────────────────────────────────

install_cli_tools() {
    print_header "Installing CLI Tools"

    CLI_TOOLS=(
        # Version Control
        "git"
        "gh"                # GitHub CLI

        # Languages & Runtimes
        "node"
        "python"
        "go"

        # Shell & Terminal
        "zsh"
        "starship"          # Prompt
        "fzf"               # Fuzzy finder
        "ripgrep"           # Better grep
        "bat"               # Better cat
        "eza"               # Better ls
        "fd"                # Better find
        "jq"                # JSON processor
        "yq"                # YAML processor

        # Networking
        "curl"
        "wget"
        "httpie"

        # Development
        "tree"
        "watch"
        "htop"
        "tldr"              # Simplified man pages
    )

    for tool in "${CLI_TOOLS[@]}"; do
        if brew list "$tool" &>/dev/null; then
            print_success "$tool already installed"
        else
            print_step "Installing $tool..."
            brew install "$tool"
        fi
    done
}

# ─────────────────────────────────────────────────────────────────────────────
# 4. GUI APPLICATIONS
# ─────────────────────────────────────────────────────────────────────────────

install_gui_apps() {
    print_header "Installing GUI Applications"

    GUI_APPS=(
        # Development
        "visual-studio-code"
        "iterm2"
        "docker"

        # Browsers
        "google-chrome"
        "firefox"

        # Productivity
        "rectangle"         # Window management
        "alfred"            # Spotlight replacement

        # Communication
        "slack"
        "zoom"

        # Utilities
        "the-unarchiver"
    )

    for app in "${GUI_APPS[@]}"; do
        if brew list --cask "$app" &>/dev/null; then
            print_success "$app already installed"
        else
            print_step "Installing $app..."
            brew install --cask "$app"
        fi
    done
}

# ─────────────────────────────────────────────────────────────────────────────
# 5. FONTS
# ─────────────────────────────────────────────────────────────────────────────

install_fonts() {
    print_header "Installing Fonts"

    FONTS=(
        "font-fira-code"
        "font-jetbrains-mono"
        "font-meslo-lg-nerd-font"
    )

    for font in "${FONTS[@]}"; do
        if brew list --cask "$font" &>/dev/null; then
            print_success "$font already installed"
        else
            print_step "Installing $font..."
            brew install --cask "$font"
        fi
    done
}

# ─────────────────────────────────────────────────────────────────────────────
# 6. SHELL CONFIGURATION
# ─────────────────────────────────────────────────────────────────────────────

configure_shell() {
    print_header "Configuring Shell"

    # Install Oh My Zsh if not present
    if [ ! -d "$HOME/.oh-my-zsh" ]; then
        print_step "Installing Oh My Zsh..."
        sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended
    else
        print_success "Oh My Zsh already installed"
    fi

    # Create .zshrc additions
    print_step "Adding shell configurations..."

    cat >> ~/.zshrc << 'ZSHRC'

# ─────────────────────────────────────────────────────────────────────────────
# DEV TOOLKIT ADDITIONS
# ─────────────────────────────────────────────────────────────────────────────

# Starship prompt
eval "$(starship init zsh)"

# Better ls
alias ls="eza"
alias ll="eza -la"
alias la="eza -la"
alias lt="eza --tree"

# Better cat
alias cat="bat"

# Better grep
alias grep="rg"

# Better find
alias find="fd"

# Git shortcuts
alias gs="git status"
alias gd="git diff"
alias gl="git log --oneline -20"
alias gp="git push"
alias gc="git commit"

# Docker shortcuts
alias dps="docker ps"
alias dpa="docker ps -a"
alias di="docker images"

# Quick navigation
alias ..="cd .."
alias ...="cd ../.."
alias ....="cd ../../.."

# fzf configuration
[ -f ~/.fzf.zsh ] && source ~/.fzf.zsh
export FZF_DEFAULT_COMMAND='fd --type f --hidden --follow --exclude .git'
export FZF_CTRL_T_COMMAND="$FZF_DEFAULT_COMMAND"

# Node version manager (if using nvm)
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"

ZSHRC

    print_success "Shell configured"
}

# ─────────────────────────────────────────────────────────────────────────────
# 7. GIT CONFIGURATION
# ─────────────────────────────────────────────────────────────────────────────

configure_git() {
    print_header "Configuring Git"

    # Check if already configured
    if git config --global user.email &>/dev/null; then
        print_success "Git already configured"
        print_step "Current: $(git config --global user.name) <$(git config --global user.email)>"
    else
        print_step "Setting up Git..."

        echo "Enter your Git name: "
        read -r git_name
        echo "Enter your Git email: "
        read -r git_email

        git config --global user.name "$git_name"
        git config --global user.email "$git_email"
    fi

    # Set useful defaults
    git config --global init.defaultBranch main
    git config --global pull.rebase false
    git config --global core.editor "code --wait"
    git config --global push.autoSetupRemote true

    # Useful aliases
    git config --global alias.st status
    git config --global alias.co checkout
    git config --global alias.br branch
    git config --global alias.ci commit
    git config --global alias.last "log -1 HEAD"
    git config --global alias.unstage "reset HEAD --"
    git config --global alias.visual "!gitk"

    print_success "Git configured"
}

# ─────────────────────────────────────────────────────────────────────────────
# 8. SSH KEY
# ─────────────────────────────────────────────────────────────────────────────

setup_ssh() {
    print_header "Setting Up SSH Key"

    SSH_KEY="$HOME/.ssh/id_ed25519"

    if [ -f "$SSH_KEY" ]; then
        print_success "SSH key already exists"
    else
        print_step "Generating SSH key..."

        echo "Enter your email for SSH key: "
        read -r ssh_email

        ssh-keygen -t ed25519 -C "$ssh_email" -f "$SSH_KEY" -N ""

        # Start ssh-agent and add key
        eval "$(ssh-agent -s)"
        ssh-add "$SSH_KEY"

        # Copy to clipboard
        pbcopy < "$SSH_KEY.pub"

        print_success "SSH key generated and copied to clipboard"
        echo ""
        echo "Add this key to GitHub: https://github.com/settings/keys"
        echo "Press Enter when done..."
        read -r
    fi
}

# ─────────────────────────────────────────────────────────────────────────────
# 9. VS CODE EXTENSIONS
# ─────────────────────────────────────────────────────────────────────────────

install_vscode_extensions() {
    print_header "Installing VS Code Extensions"

    if ! command_exists code; then
        print_warning "VS Code CLI not found, skipping extensions"
        return
    fi

    EXTENSIONS=(
        # Languages
        "ms-python.python"
        "golang.go"
        "dbaeumer.vscode-eslint"
        "esbenp.prettier-vscode"

        # Git
        "eamodio.gitlens"
        "mhutchie.git-graph"

        # Docker
        "ms-azuretools.vscode-docker"

        # Utilities
        "streetsidesoftware.code-spell-checker"
        "usernamehw.errorlens"
        "christian-kohler.path-intellisense"

        # Themes
        "dracula-theme.theme-dracula"
        "pkief.material-icon-theme"
    )

    for ext in "${EXTENSIONS[@]}"; do
        print_step "Installing $ext..."
        code --install-extension "$ext" --force 2>/dev/null || true
    done

    print_success "VS Code extensions installed"
}

# ─────────────────────────────────────────────────────────────────────────────
# 10. MACOS PREFERENCES
# ─────────────────────────────────────────────────────────────────────────────

configure_macos() {
    print_header "Configuring macOS Preferences"

    # Show hidden files in Finder
    defaults write com.apple.finder AppleShowAllFiles -bool true

    # Show path bar in Finder
    defaults write com.apple.finder ShowPathbar -bool true

    # Show status bar in Finder
    defaults write com.apple.finder ShowStatusBar -bool true

    # Keep folders on top when sorting by name
    defaults write com.apple.finder _FXSortFoldersFirst -bool true

    # Disable the warning when changing a file extension
    defaults write com.apple.finder FXEnableExtensionChangeWarning -bool false

    # Use list view in all Finder windows by default
    defaults write com.apple.finder FXPreferredViewStyle -string "Nlsv"

    # Enable tap to click
    defaults write com.apple.driver.AppleBluetoothMultitouch.trackpad Clicking -bool true

    # Fast key repeat
    defaults write NSGlobalDomain KeyRepeat -int 2
    defaults write NSGlobalDomain InitialKeyRepeat -int 15

    # Disable auto-correct
    defaults write NSGlobalDomain NSAutomaticSpellingCorrectionEnabled -bool false

    # Require password immediately after sleep
    defaults write com.apple.screensaver askForPassword -int 1
    defaults write com.apple.screensaver askForPasswordDelay -int 0

    # Restart Finder to apply changes
    killall Finder

    print_success "macOS preferences configured"
}

# ─────────────────────────────────────────────────────────────────────────────
# 11. CLEANUP & FINISH
# ─────────────────────────────────────────────────────────────────────────────

cleanup() {
    print_header "Cleaning Up"

    brew cleanup

    print_success "Cleanup complete"
}

show_summary() {
    print_header "Setup Complete!"

    echo "Installed:"
    echo "  - Xcode Command Line Tools"
    echo "  - Homebrew"
    echo "  - CLI tools (git, node, python, etc.)"
    echo "  - GUI apps (VS Code, iTerm2, Docker, etc.)"
    echo "  - Fonts (Fira Code, JetBrains Mono)"
    echo "  - Oh My Zsh + Starship prompt"
    echo "  - VS Code extensions"
    echo ""
    echo "Configured:"
    echo "  - Git (name, email, aliases)"
    echo "  - SSH key"
    echo "  - Shell aliases"
    echo "  - macOS preferences"
    echo ""
    echo -e "${YELLOW}Next steps:${NC}"
    echo "  1. Restart your terminal (or run: source ~/.zshrc)"
    echo "  2. Log in to GitHub CLI: gh auth login"
    echo "  3. Log in to Docker Desktop"
    echo "  4. Configure iTerm2 to use Meslo Nerd Font"
    echo ""
    print_success "Happy coding!"
}

# ─────────────────────────────────────────────────────────────────────────────
# MAIN
# ─────────────────────────────────────────────────────────────────────────────

main() {
    print_header "DEV ENVIRONMENT SETUP"
    echo "This script will set up your development environment."
    echo "It may take 15-30 minutes depending on your internet connection."
    echo ""
    echo "Press Enter to continue (or Ctrl+C to cancel)..."
    read -r

    install_xcode_tools
    install_homebrew
    install_cli_tools
    install_gui_apps
    install_fonts
    configure_shell
    configure_git
    setup_ssh
    install_vscode_extensions
    configure_macos
    cleanup
    show_summary
}

# Run it!
main
```

---

## Customization

### Modify the Package Lists

Edit these arrays to customize what gets installed:

```bash
# CLI tools
CLI_TOOLS=(
    "git"
    "node"
    # Add your tools here
)

# GUI applications
GUI_APPS=(
    "visual-studio-code"
    # Add your apps here
)
```

### Skip Sections

Comment out function calls in `main()`:

```bash
main() {
    install_homebrew
    install_cli_tools
    # install_gui_apps    # Skip GUI apps
    configure_git
    # configure_macos     # Keep macOS defaults
}
```

### Add Your Own Steps

Add new functions and call them from `main()`:

```bash
install_my_stuff() {
    print_header "Installing My Custom Tools"
    brew install my-tool
}

main() {
    # ...existing steps...
    install_my_stuff
}
```

---

## Using a Brewfile Instead

For more complex setups, use a Brewfile:

### Brewfile

```ruby
# Brewfile - Install with: brew bundle

# Taps
tap "homebrew/cask-fonts"

# CLI Tools
brew "git"
brew "gh"
brew "node"
brew "python"
brew "go"
brew "fzf"
brew "ripgrep"
brew "bat"
brew "eza"
brew "jq"
brew "starship"

# Applications
cask "visual-studio-code"
cask "iterm2"
cask "docker"
cask "google-chrome"
cask "rectangle"
cask "slack"

# Fonts
cask "font-fira-code"
cask "font-jetbrains-mono"
cask "font-meslo-lg-nerd-font"

# Mac App Store (requires mas)
# mas "Xcode", id: 497799835
```

### Install from Brewfile

```bash
brew bundle install
```

### Export Current Setup

```bash
brew bundle dump --file=~/Brewfile
```

---

## Gotchas

### Apple Silicon vs Intel

The script handles this automatically, but be aware:

```bash
# Apple Silicon (M1/M2/M3)
Homebrew path: /opt/homebrew

# Intel
Homebrew path: /usr/local
```

### Xcode Installation Dialog

The script will pause and wait for you to click through the Xcode dialog. This is unavoidable.

### SSH Key

The script generates an SSH key and copies it to clipboard. You still need to:
1. Go to GitHub Settings → SSH Keys
2. Paste the key
3. Press Enter to continue the script

### VS Code CLI

If `code` command isn't found:
1. Open VS Code
2. Cmd+Shift+P → "Shell Command: Install 'code' command in PATH"
3. Restart terminal

---

## Recovery

### Something Broke?

```bash
# Reset Homebrew
brew cleanup --prune=all
brew doctor

# Reinstall a tool
brew reinstall <package>

# Reset shell config
cp ~/.zshrc ~/.zshrc.backup
# Then edit ~/.zshrc and remove the DEV TOOLKIT ADDITIONS section
```

### Start Fresh

```bash
# Uninstall everything
brew list | xargs brew uninstall --force
brew list --cask | xargs brew uninstall --cask --force

# Uninstall Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"
```

---

*One script. Complete dev environment. Go build something.*
