# Package Manager Troubleshooting

> node_modules nightmares solved.

## Table of Contents
- [npm Errors](#npm-errors)
- [Yarn Errors](#yarn-errors)
- [pnpm Errors](#pnpm-errors)
- [General Fixes](#general-fixes)

---

## npm Errors

### EACCES: permission denied

```
npm ERR! Error: EACCES: permission denied, access '/usr/local/lib/node_modules'
```

**Fix:**
```bash
# Option 1: Change npm's default directory
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.zshrc
source ~/.zshrc

# Option 2: Use nvm (recommended)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
```

**Never use:** `sudo npm install -g`

---

### ENOENT: no such file or directory

```
npm ERR! enoent ENOENT: no such file or directory, open 'package.json'
```

**Fix:**
```bash
# Are you in the right directory?
ls package.json

# Create if missing
npm init -y
```

---

### Peer dependency conflicts

```
npm ERR! Could not resolve dependency:
npm ERR! peer react@"^17.0.0" from package@1.0.0
```

**Fix:**
```bash
# Force install (may cause issues)
npm install --legacy-peer-deps

# Or use exact versions
npm install react@17.0.2
```

---

### ERESOLVE unable to resolve

```
npm ERR! ERESOLVE unable to resolve dependency tree
```

**Fix:**
```bash
# Option 1: Use legacy algorithm
npm install --legacy-peer-deps

# Option 2: Force
npm install --force

# Option 3: Clean and retry
rm -rf node_modules package-lock.json
npm install
```

---

### npm cache issues

```
npm ERR! code EINTEGRITY
npm ERR! Integrity checksum failed
```

**Fix:**
```bash
npm cache clean --force
rm -rf node_modules package-lock.json
npm install
```

---

### Module not found

```
Error: Cannot find module 'lodash'
```

**Fix:**
```bash
# Reinstall
npm install

# If specific package
npm install lodash

# Nuclear option
rm -rf node_modules package-lock.json
npm install
```

---

## Yarn Errors

### ENOENT when running yarn

```
error An unexpected error occurred: "ENOENT: no such file or directory"
```

**Fix:**
```bash
yarn cache clean
rm -rf node_modules yarn.lock
yarn install
```

---

### Integrity check failed

```
error Integrity check failed
```

**Fix:**
```bash
yarn cache clean
yarn install --check-files
```

---

### Package not found in registry

```
error Couldn't find package "my-package" on the "npm" registry
```

**Fix:**
```bash
# Check registry
yarn config get registry

# Reset to npm
yarn config set registry https://registry.npmjs.org/
```

---

### Workspace conflicts

```
error Workspaces can only be enabled in private projects
```

**Fix:**

Add to root package.json:
```json
{
  "private": true
}
```

---

## pnpm Errors

### ERR_PNPM_PEER_DEP_ISSUES

```
ERR_PNPM_PEER_DEP_ISSUES  Unmet peer dependencies
```

**Fix:**
```bash
# Allow auto-install of peers
pnpm install --auto-install-peers

# Or add to .npmrc
echo "auto-install-peers=true" >> .npmrc
```

---

### Strict mode issues

```
ERR_PNPM_NO_MATCHING_VERSION
```

pnpm is stricter about dependency resolution.

**Fix:**
```bash
# Check what's conflicting
pnpm why package-name

# Override in package.json
{
  "pnpm": {
    "overrides": {
      "package-name": "^1.0.0"
    }
  }
}
```

---

### Store corruption

```
ERR_PNPM_UNEXPECTED_STORE
```

**Fix:**
```bash
pnpm store prune
rm -rf node_modules
pnpm install
```

---

## General Fixes

### The Nuclear Option

When nothing else works:

```bash
# Remove everything
rm -rf node_modules
rm package-lock.json yarn.lock pnpm-lock.yaml

# Clear all caches
npm cache clean --force
yarn cache clean
pnpm store prune

# Reinstall
npm install  # or yarn / pnpm install
```

### Check Node Version

Many issues come from wrong Node version:

```bash
node --version

# Use nvm to switch
nvm install 18
nvm use 18
```

### Upgrade Package Manager

```bash
npm install -g npm@latest
npm install -g yarn@latest
npm install -g pnpm@latest
```

### Check Registry

```bash
# npm
npm config get registry

# Reset
npm config set registry https://registry.npmjs.org/
```

### Proxy Issues

If behind a corporate proxy:

```bash
npm config set proxy http://proxy.company.com:8080
npm config set https-proxy http://proxy.company.com:8080
```

### SSL Certificate Issues

```bash
# Temporary fix (not recommended for production)
npm config set strict-ssl false

# Better: add certificate
npm config set cafile /path/to/certificate.pem
```

---

## Quick Fix Checklist

When packages won't install:

1. ☐ Are you in the right directory? (`ls package.json`)
2. ☐ Is Node running? (`node --version`)
3. ☐ Try clearing cache (`npm cache clean --force`)
4. ☐ Delete node_modules and lock file
5. ☐ Reinstall (`npm install`)
6. ☐ Check Node version matches project requirements
7. ☐ Try `--legacy-peer-deps` for peer conflicts
8. ☐ Nuclear: reinstall Node via nvm

---

*90% of npm problems are solved by deleting node_modules and reinstalling.*
