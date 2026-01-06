# Homebrew

> macOS package manager. Install anything with one command.

## Table of Contents
- [Installation](#installation)
- [Daily Commands](#daily-commands)
- [Finding Packages](#finding-packages)
- [Managing Packages](#managing-packages)
- [Casks (GUI Apps)](#casks-gui-apps)
- [Maintenance](#maintenance)
- [Troubleshooting](#troubleshooting)
- [Real-World Workflows](#real-world-workflows)

---

## Installation

### Install Homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### After Installation (Apple Silicon)

```bash
# Add to your ~/.zshrc
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
source ~/.zshrc
```

### Verify Installation

```bash
brew doctor
```

---

## Daily Commands

### Install a Package

```bash
brew install <package>
```

```bash
# Examples
brew install git
brew install node
brew install python
brew install wget
```

### Uninstall a Package

```bash
brew uninstall <package>
```

### Update Everything

```bash
# Update Homebrew itself + package list
brew update

# Upgrade all installed packages
brew upgrade

# Do both
brew update && brew upgrade
```

### See What's Installed

```bash
brew list
```

### Check if Something is Installed

```bash
brew list | grep <package>

# Or
brew ls --versions <package>
```

---

## Finding Packages

### Search for a Package

```bash
brew search <name>
```

```bash
# Examples
brew search postgres
brew search python
brew search video    # Partial matches work
```

### Get Package Info

```bash
brew info <package>
```

Shows: version, dependencies, install size, description.

### See What a Package Provides

```bash
brew info --json <package> | jq '.[] | .linked_keg'
```

### Browse All Formulas

```bash
# Opens in browser
brew home
```

---

## Managing Packages

### See Outdated Packages

```bash
brew outdated
```

### Upgrade Specific Package

```bash
brew upgrade <package>
```

### Pin a Package (Prevent Upgrades)

```bash
brew pin <package>
brew unpin <package>
```

### Switch Package Version

```bash
# List available versions
brew list --versions <package>

# Install specific version (if available)
brew install <package>@<version>

# Example
brew install node@18
brew install python@3.11
```

### Link/Unlink Packages

```bash
# Make package available in PATH
brew link <package>

# Remove from PATH (but keep installed)
brew unlink <package>

# Force link (overwrite conflicts)
brew link --overwrite <package>
```

### See Dependencies

```bash
# What does this package need?
brew deps <package>

# What depends on this package?
brew uses --installed <package>

# Tree view
brew deps --tree <package>
```

---

## Casks (GUI Apps)

Casks are for GUI applications (.app files).

### Install a GUI App

```bash
brew install --cask <app>
```

```bash
# Examples
brew install --cask visual-studio-code
brew install --cask google-chrome
brew install --cask slack
brew install --cask docker
brew install --cask iterm2
brew install --cask rectangle    # Window manager
brew install --cask alfred       # Spotlight replacement
```

### List Installed Casks

```bash
brew list --cask
```

### Upgrade Casks

```bash
# Upgrade all casks
brew upgrade --cask

# Upgrade specific cask
brew upgrade --cask <app>
```

### Uninstall Cask

```bash
brew uninstall --cask <app>
```

### Search for Casks

```bash
brew search --cask <name>
```

---

## Maintenance

### Clean Up Old Versions

```bash
# Remove old versions of installed formulas
brew cleanup

# See what would be removed
brew cleanup -n

# Remove everything older than N days
brew cleanup --prune=30
```

### Check for Problems

```bash
brew doctor
```

Fix issues it reports before they become problems.

### See What's Taking Space

```bash
# Size of each package
brew list --formula | xargs -I {} sh -c 'echo -n "{}: "; du -sh $(brew --cellar)/{}' | sort -h -k2

# Quick summary
du -sh $(brew --cache)
du -sh $(brew --cellar)
```

### Clear Download Cache

```bash
brew cleanup --prune=all
rm -rf $(brew --cache)
```

### Repair Permissions

```bash
sudo chown -R $(whoami) $(brew --prefix)/*
```

---

## Troubleshooting

### Problem: "command not found" after install

**Cause:** Package not linked or shell not reloaded.

```bash
# Try linking
brew link <package>

# Reload shell
source ~/.zshrc

# Check if it's a keg-only formula
brew info <package> | grep "keg-only"
```

### Problem: "Error: <package> is already installed"

```bash
# Reinstall
brew reinstall <package>
```

### Problem: Permission denied

```bash
# Fix Homebrew permissions
sudo chown -R $(whoami) /opt/homebrew

# Or for Intel Macs
sudo chown -R $(whoami) /usr/local/Homebrew
```

### Problem: Conflicts between packages

```bash
# See what's conflicting
brew doctor

# Unlink the conflicting one
brew unlink <package>

# Or use the version-specific formula
brew install <package>@<version>
```

### Problem: Cask already installed (but missing)

```bash
# Force reinstall
brew reinstall --cask <app>

# Or uninstall first
brew uninstall --cask <app>
brew install --cask <app>
```

### Problem: Slow updates

```bash
# Use a mirror (China)
export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git"

# Or just update less frequently
```

### Problem: "SHA256 mismatch"

```bash
# Clear cache and retry
rm -rf $(brew --cache)
brew install <package>
```

---

## Real-World Workflows

### Scenario: Setting Up a New Mac

```bash
# Install Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Add to path (Apple Silicon)
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
source ~/.zshrc

# Install essentials
brew install git
brew install node
brew install python
brew install wget
brew install jq
brew install gh           # GitHub CLI
brew install fzf          # Fuzzy finder
brew install ripgrep      # Better grep
brew install bat          # Better cat
brew install eza          # Better ls

# Install GUI apps
brew install --cask visual-studio-code
brew install --cask iterm2
brew install --cask rectangle
brew install --cask docker
```

### Scenario: Export/Import Package List

```bash
# Export what you have installed
brew bundle dump

# This creates a Brewfile with all packages

# On new machine, install everything
brew bundle install
```

**Brewfile format:**
```ruby
tap "homebrew/cask"
brew "git"
brew "node"
brew "python"
cask "visual-studio-code"
cask "docker"
```

### Scenario: Find What Installed a File

```bash
# What package owns this file?
brew list --formula | while read f; do
  brew ls "$f" | grep -q "/path/to/file" && echo "$f"
done

# Or search by command name
which <command>
# /opt/homebrew/bin/rg

ls -la /opt/homebrew/bin/rg
# Shows symlink to ../Cellar/ripgrep/...
```

### Scenario: Install Multiple Things at Once

```bash
# Multiple packages
brew install git node python wget jq

# Multiple casks
brew install --cask visual-studio-code iterm2 docker
```

### Scenario: Keep Mac Updated (Weekly Routine)

```bash
# Update and upgrade everything
brew update && brew upgrade && brew upgrade --cask

# Clean up old versions
brew cleanup

# Check for issues
brew doctor
```

### Scenario: Roll Back a Package

```bash
# See installed versions
brew list --versions <package>

# Unlink current
brew unlink <package>

# Link specific version
brew link <package>@<version>

# Or if you need to reinstall old version
brew install <package>@<version>
```

### Scenario: Services (Background Daemons)

```bash
# Start a service
brew services start <package>

# Stop a service
brew services stop <package>

# Restart
brew services restart <package>

# See all services
brew services list

# Examples
brew services start postgresql
brew services start redis
brew services start mysql
```

---

## Quick Reference

| Command | Action |
|---------|--------|
| `brew install <pkg>` | Install package |
| `brew uninstall <pkg>` | Remove package |
| `brew upgrade` | Upgrade all packages |
| `brew update` | Update Homebrew itself |
| `brew list` | List installed |
| `brew search <name>` | Search packages |
| `brew info <pkg>` | Package info |
| `brew doctor` | Check for problems |
| `brew cleanup` | Remove old versions |
| `brew install --cask <app>` | Install GUI app |
| `brew services start <pkg>` | Start background service |

---

## Essential Packages

| Package | What it does |
|---------|--------------|
| `git` | Version control |
| `node` | JavaScript runtime |
| `python` | Python 3 |
| `wget` / `curl` | Download files |
| `jq` | JSON processor |
| `gh` | GitHub CLI |
| `fzf` | Fuzzy finder |
| `ripgrep` | Fast grep |
| `bat` | Better cat |
| `eza` | Better ls |
| `htop` | Process viewer |
| `tldr` | Simplified man pages |
| `tree` | Directory tree |
| `watch` | Run command repeatedly |

---

*Homebrew = one command to install almost anything on macOS.*
