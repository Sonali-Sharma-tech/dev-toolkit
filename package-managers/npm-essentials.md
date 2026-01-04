# npm Essentials

The commands you'll use every day with npm.

## Table of Contents
- [Real-World Workflows](#real-world-workflows)
- [Quick Reference](#quick-reference)
- [Installing Packages](#installing-packages)
- [Managing Dependencies](#managing-dependencies)
- [Scripts](#scripts)
- [Versioning](#versioning)
- [Publishing](#publishing)
- [Troubleshooting](#troubleshooting)
- [npm vs yarn vs pnpm](#npm-vs-yarn-vs-pnpm)

---

## Real-World Workflows

### Scenario 1: "Starting on a New Project"

**Situation:** You just cloned a repo and need to get it running.

```bash
# Step 1: Install dependencies
npm install
# or for exact versions from lock file (CI/CD recommended)
npm ci

# Step 2: Check what scripts are available
npm run

# Step 3: Usually one of these
npm run dev
npm start
npm run serve
```

---

### Scenario 2: "Adding a New Library to Your Project"

**Situation:** You need to add axios for API calls.

```bash
# Step 1: Search for the package (optional)
npm search axios

# Step 2: Check package info
npm info axios

# Step 3: Install it
npm install axios

# Step 4: If it's a dev tool (testing, linting, etc.)
npm install -D jest
npm install -D eslint prettier
```

---

### Scenario 3: "Project Won't Start After Pulling Latest Changes"

**Situation:** Teammate added new dependencies, your project is broken.

```bash
# Step 1: Clean reinstall
rm -rf node_modules
npm install

# Step 2: If still broken, try with lock file
rm -rf node_modules package-lock.json
npm install

# Step 3: Check for peer dependency issues
npm ls 2>&1 | grep "peer dep"
```

---

### Scenario 4: "Updating Dependencies for Security"

**Situation:** GitHub is showing security vulnerabilities.

```bash
# Step 1: See what's vulnerable
npm audit

# Step 2: Auto-fix what's safe
npm audit fix

# Step 3: See what still needs manual update
npm outdated

# Step 4: Update specific package
npm update lodash

# Step 5: Update to latest (may have breaking changes)
npm install lodash@latest
```

---

### Scenario 5: "Setting Up a New Project from Scratch"

**Situation:** Starting a brand new project.

```bash
# Step 1: Create project folder
mkdir my-project && cd my-project

# Step 2: Initialize package.json
npm init -y

# Step 3: Add your dependencies
npm install express
npm install -D nodemon typescript @types/node

# Step 4: Add scripts to package.json
# "dev": "nodemon src/index.ts"
# "build": "tsc"
# "start": "node dist/index.js"
```

---

### Scenario 6: "Running a One-Off Tool Without Installing"

**Situation:** You need to use create-react-app or a CLI tool once.

```bash
# Use npx - runs without permanent install
npx create-react-app my-app
npx create-next-app my-next-app
npx degit user/repo my-project

# Run specific version
npx cowsay@1.5.0 "Hello"

# Run from GitHub
npx github:piuccio/cowsay "Moo"
```

---

### Scenario 7: "CI/CD Pipeline Setup"

**Situation:** Setting up automated builds.

```bash
# Always use npm ci in CI/CD - it's faster and deterministic
npm ci

# For production builds
npm ci --production  # Skip devDependencies

# Run tests
npm test

# Build
npm run build
```

**Example GitHub Actions:**
```yaml
- name: Install dependencies
  run: npm ci

- name: Run tests
  run: npm test

- name: Build
  run: npm run build
```

---

## Quick Reference

```bash
npm install              # Install all dependencies
npm install package      # Add package to dependencies
npm install -D package   # Add to devDependencies
npm uninstall package    # Remove package
npm update               # Update packages
npm run script           # Run a script
npm list                 # Show installed packages
npm outdated             # Show outdated packages
npm cache clean --force  # Clear cache
```

---

## Installing Packages

### Install All Dependencies
```bash
# Install from package.json
npm install
npm i                    # Shorthand

# Clean install (delete node_modules first)
npm ci                   # Faster, uses package-lock.json exactly

# Install for production only
npm install --production
npm ci --production
```

### Add a Package
```bash
# Add to dependencies
npm install lodash
npm i lodash             # Shorthand

# Add to devDependencies
npm install -D jest
npm install --save-dev jest

# Add specific version
npm install lodash@4.17.21

# Add latest version
npm install lodash@latest

# Add from GitHub
npm install github:user/repo
npm install github:user/repo#branch

# Add from local path
npm install ../my-local-package
npm install file:../my-local-package
```

### Global Packages
```bash
# Install globally
npm install -g typescript
npm install -g @angular/cli

# List global packages
npm list -g --depth=0

# Where are global packages?
npm root -g

# Uninstall global
npm uninstall -g typescript
```

### Install Options
```bash
# Don't save to package.json
npm install lodash --no-save

# Save exact version (no ^)
npm install lodash --save-exact

# Install optional dependencies
npm install --include=optional

# Skip optional dependencies
npm install --omit=optional
```

---

## Managing Dependencies

### View Dependencies
```bash
# List all installed packages
npm list
npm ls                   # Shorthand

# Top-level only
npm list --depth=0

# Specific package
npm list lodash

# Why is this installed?
npm explain lodash
npm why lodash           # Shorthand
```

### Update Packages
```bash
# Check for outdated
npm outdated

# Update all packages
npm update

# Update specific package
npm update lodash

# Update to latest (ignores semver)
npm install lodash@latest
```

### Remove Packages
```bash
# Remove package
npm uninstall lodash
npm remove lodash        # Alias
npm rm lodash            # Shorthand

# Remove from devDependencies
npm uninstall -D jest

# Remove without updating package.json
npm uninstall lodash --no-save
```

### Audit Security
```bash
# Check for vulnerabilities
npm audit

# Auto-fix vulnerabilities
npm audit fix

# Force fix (may include breaking changes)
npm audit fix --force

# Just show what would be fixed
npm audit fix --dry-run
```

---

## Scripts

### package.json Scripts
```json
{
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "build": "webpack --mode production",
    "test": "jest",
    "lint": "eslint src/",
    "pretest": "npm run lint",
    "postbuild": "echo 'Build complete!'"
  }
}
```

### Running Scripts
```bash
# Run a script
npm run dev
npm run build
npm run test

# Built-in shortcuts
npm start                # Same as npm run start
npm test                 # Same as npm run test
npm t                    # Shorthand for test

# Run with arguments
npm run build -- --watch
npm test -- --coverage

# List available scripts
npm run
```

### Pre/Post Scripts
```bash
# These run automatically
preinstall    # Before npm install
postinstall   # After npm install
pretest       # Before npm test
posttest      # After npm test
prebuild      # Before npm run build
postbuild     # After npm run build
```

### Passing Arguments
```bash
# In package.json
"scripts": {
  "greet": "echo Hello"
}

# Running with args
npm run greet -- World
# Output: Hello World
```

---

## Versioning

### Understanding Versions
```
1.2.3
│ │ │
│ │ └── Patch (bug fixes)
│ └──── Minor (new features, backwards compatible)
└────── Major (breaking changes)
```

### Version Ranges
```json
{
  "dependencies": {
    "exact": "1.2.3",        // Exactly 1.2.3
    "caret": "^1.2.3",       // >=1.2.3 <2.0.0 (default)
    "tilde": "~1.2.3",       // >=1.2.3 <1.3.0
    "range": ">=1.0.0 <2.0.0",
    "any": "*",              // Any version
    "latest": "latest"       // Latest version
  }
}
```

### Version Commands
```bash
# Check current version
npm version

# Bump version
npm version patch          # 1.0.0 → 1.0.1
npm version minor          # 1.0.0 → 1.1.0
npm version major          # 1.0.0 → 2.0.0

# Set specific version
npm version 2.0.0

# Bump without git tag
npm version patch --no-git-tag-version
```

### Package Info
```bash
# View package info
npm info lodash
npm view lodash            # Alias

# View all versions
npm view lodash versions

# View specific field
npm view lodash version
npm view lodash dependencies
```

---

## Publishing

### Prepare to Publish
```bash
# Login to npm
npm login

# Check who you're logged in as
npm whoami

# Initialize package.json
npm init
npm init -y                # Accept defaults
```

### Publishing
```bash
# Publish package
npm publish

# Publish with specific tag
npm publish --tag beta

# Publish scoped package as public
npm publish --access public

# Dry run (don't actually publish)
npm publish --dry-run
```

### Unpublishing
```bash
# Unpublish specific version
npm unpublish package@1.0.0

# Unpublish entire package (within 72 hours)
npm unpublish package --force
```

### .npmignore
```
# Files to exclude from package
node_modules/
tests/
.env
*.log
.git/
```

---

## Troubleshooting

### Clear Cache
```bash
# Clear npm cache
npm cache clean --force

# Verify cache
npm cache verify
```

### Fix node_modules
```bash
# Nuclear option: delete and reinstall
rm -rf node_modules package-lock.json
npm install

# Or use npm ci for clean install
rm -rf node_modules
npm ci
```

### Permission Errors (macOS/Linux)
```bash
# Fix npm permissions (recommended)
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.zshrc
source ~/.zshrc

# Or use npx for one-off commands
npx create-react-app my-app
```

### Common Errors

**EACCES: permission denied**
```bash
# Don't use sudo! Fix permissions instead
sudo chown -R $(whoami) ~/.npm
```

**ERESOLVE: dependency conflict**
```bash
# Try with legacy peer deps
npm install --legacy-peer-deps

# Or force (not recommended)
npm install --force
```

**ENOENT: no such file or directory**
```bash
# Usually means corrupted install
rm -rf node_modules package-lock.json
npm install
```

**Package not found**
```bash
# Check if it exists
npm view package-name

# Check your registry
npm config get registry

# Reset to default registry
npm config set registry https://registry.npmjs.org/
```

---

## npm vs yarn vs pnpm

### Command Comparison

| npm | yarn | pnpm | What it does |
|-----|------|------|--------------|
| `npm install` | `yarn` | `pnpm install` | Install all |
| `npm install pkg` | `yarn add pkg` | `pnpm add pkg` | Add package |
| `npm install -D pkg` | `yarn add -D pkg` | `pnpm add -D pkg` | Add dev dep |
| `npm uninstall pkg` | `yarn remove pkg` | `pnpm remove pkg` | Remove |
| `npm update` | `yarn upgrade` | `pnpm update` | Update all |
| `npm run script` | `yarn script` | `pnpm script` | Run script |
| `npm ci` | `yarn install --frozen-lockfile` | `pnpm install --frozen-lockfile` | CI install |
| `npx pkg` | `yarn dlx pkg` | `pnpm dlx pkg` | Run package |

### When to Use Which

| Tool | Best For |
|------|----------|
| **npm** | Default choice, maximum compatibility |
| **yarn** | Better workspaces, faster than npm |
| **pnpm** | Disk space savings, fastest, strictest |

### Quick Setup

**yarn:**
```bash
npm install -g yarn
yarn --version
```

**pnpm:**
```bash
npm install -g pnpm
pnpm --version
```

---

## npx (Run Without Installing)

```bash
# Run package without installing
npx create-react-app my-app
npx cowsay "Hello"

# Run specific version
npx lodash@4.17.0 --version

# Run from GitHub
npx github:user/repo

# Run local package
npx ./my-local-package
```

---

## Useful Config

```bash
# View all config
npm config list

# Set default author
npm config set init-author-name "Your Name"
npm config set init-author-email "you@email.com"

# Set default license
npm config set init-license "MIT"

# Save exact versions by default
npm config set save-exact true

# Set registry
npm config set registry https://registry.npmjs.org/
```

