# npm Error Messages Decoded

When npm throws an error, find it here.

## Table of Contents
- [EACCES: permission denied](#eacces-permission-denied)
- [ENOENT: no such file or directory](#enoent-no-such-file-or-directory)
- [ERESOLVE: unable to resolve dependency tree](#eresolve-unable-to-resolve-dependency-tree)
- [EINTEGRITY: integrity check failed](#eintegrity-integrity-check-failed)
- [ENOTFOUND: request failed](#enotfound-request-failed)
- [ERR_SOCKET_TIMEOUT: network timeout](#err_socket_timeout-network-timeout)
- [EPERM: operation not permitted](#eperm-operation-not-permitted)
- [code ELIFECYCLE: script failed](#code-elifecycle-script-failed)
- [peer dep missing / peer dependency conflict](#peer-dep-missing--peer-dependency-conflict)
- [Cannot find module](#cannot-find-module)
- [cb() never called](#cb-never-called)
- [Maximum call stack size exceeded](#maximum-call-stack-size-exceeded)

---

## EACCES: permission denied

```
npm ERR! Error: EACCES: permission denied, access '/usr/local/lib/node_modules'
```

### What it means
npm is trying to write to a directory you don't have permission for.

### Quick fix
```bash
# DON'T use sudo npm! Fix the permissions instead.

# Option 1: Change npm's default directory
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.zshrc
source ~/.zshrc

# Option 2: Fix ownership of npm directories
sudo chown -R $(whoami) ~/.npm
sudo chown -R $(whoami) /usr/local/lib/node_modules
```

### Why you shouldn't use sudo
- Creates permission issues later
- Security risk
- Breaks things in unpredictable ways

---

## ENOENT: no such file or directory

```
npm ERR! enoent ENOENT: no such file or directory, open '/path/to/package.json'
```

### What it means
npm can't find a file it needs, usually package.json.

### Quick fix
```bash
# Are you in the right directory?
ls package.json

# If no package.json, initialize one
npm init -y

# If it's node_modules related, reinstall
rm -rf node_modules
npm install
```

### Other causes
```bash
# Corrupted cache
npm cache clean --force

# Then reinstall
rm -rf node_modules package-lock.json
npm install
```

---

## ERESOLVE: unable to resolve dependency tree

```
npm ERR! ERESOLVE unable to resolve dependency tree
npm ERR! Could not resolve dependency:
npm ERR! peer react@"^17.0.0" from package@1.0.0
```

### What it means
Two packages require incompatible versions of a dependency.

### Quick fix
```bash
# Try with legacy peer deps mode
npm install --legacy-peer-deps

# Or force through (may cause issues)
npm install --force
```

### Proper fix
```bash
# See what's conflicting
npm ls react

# Update the conflicting package
npm update package-name

# Or install a compatible version
npm install react@17
```

### Why this happens
- npm 7+ is stricter about peer dependencies
- Package hasn't been updated for new major versions
- Mixing incompatible package versions

---

## EINTEGRITY: integrity check failed

```
npm ERR! code EINTEGRITY
npm ERR! integrity checksum failed when using sha512
```

### What it means
The package downloaded doesn't match the checksum in package-lock.json.

### Quick fix
```bash
# Clear cache and reinstall
npm cache clean --force
rm -rf node_modules package-lock.json
npm install
```

### If that doesn't work
```bash
# Remove npm cache completely
rm -rf ~/.npm/_cacache

# Reinstall
npm install
```

### Why this happens
- Corrupted cache
- Network issues during download
- Package was updated without version bump (bad practice)

---

## ENOTFOUND: request failed

```
npm ERR! code ENOTFOUND
npm ERR! errno ENOTFOUND
npm ERR! request to https://registry.npmjs.org/package failed
```

### What it means
Can't reach the npm registry. Network/DNS issue.

### Quick fix
```bash
# Check internet connection
ping google.com
ping registry.npmjs.org

# Check registry URL
npm config get registry

# Reset to default registry
npm config set registry https://registry.npmjs.org/
```

### If behind a proxy
```bash
# Set proxy
npm config set proxy http://proxy.company.com:8080
npm config set https-proxy http://proxy.company.com:8080

# If using VPN, try disconnecting
```

---

## ERR_SOCKET_TIMEOUT: network timeout

```
npm ERR! code ERR_SOCKET_TIMEOUT
npm ERR! errno ERR_SOCKET_TIMEOUT
```

### What it means
Network is too slow or unstable.

### Quick fix
```bash
# Increase timeout
npm config set timeout 60000

# Try again
npm install
```

### Other options
```bash
# Use a different registry (mirror)
npm config set registry https://registry.npmmirror.com/

# Or use yarn/pnpm which handle network better
npx yarn install
```

---

## EPERM: operation not permitted

```
npm ERR! code EPERM
npm ERR! syscall rename
npm ERR! errno -4048
npm ERR! operation not permitted
```

### What it means
Another process is using the file, or permission issue.

### Quick fix (Windows)
```bash
# Close VS Code, IDEs, any terminal using the project

# Delete node_modules manually
rd /s /q node_modules

# Reinstall
npm install
```

### Quick fix (Mac/Linux)
```bash
# Check what's using the files
lsof +D node_modules

# Kill the process or close the app

# Then reinstall
rm -rf node_modules
npm install
```

---

## code ELIFECYCLE: script failed

```
npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! project@1.0.0 build: `webpack --mode production`
npm ERR! Exit status 1
```

### What it means
A script (build, test, etc.) failed with an error.

### Quick fix
```bash
# Run the script directly to see the actual error
npm run build

# Scroll up to find the real error
# It's usually above the npm ERR! lines
```

### Common causes
- Syntax error in code
- Missing dependency
- Wrong Node.js version
- Build configuration issue

### Debug steps
```bash
# Check Node version
node --version

# Check if dependencies are installed
npm ls

# Reinstall everything
rm -rf node_modules
npm install
```

---

## peer dep missing / peer dependency conflict

```
npm WARN peer dependency: react@^17.0.0 required by package@1.0.0
```

### What it means
A package expects another package to be installed alongside it.

### Quick fix
```bash
# Install the missing peer dependency
npm install react@17

# Or ignore peer deps (not recommended long-term)
npm install --legacy-peer-deps
```

### Understanding peer deps
```bash
# See all peer dependency warnings
npm ls 2>&1 | grep peer

# View a package's peer deps
npm info package-name peerDependencies
```

---

## Cannot find module

```
Error: Cannot find module 'lodash'
Require stack:
- /app/src/index.js
```

### What it means
Node.js can't find a package you're trying to import.

### Quick fix
```bash
# Is it installed?
npm ls lodash

# If not, install it
npm install lodash

# If it should be there, reinstall
rm -rf node_modules
npm install
```

### If it's a local file
```javascript
// Check the path is correct
// Wrong:
const utils = require('utils');

// Right (relative path):
const utils = require('./utils');
```

### Other causes
```bash
# Wrong case sensitivity (matters on Linux)
# File: Utils.js
# Import: require('./utils')  # Won't work on Linux

# Check node_modules exists
ls node_modules/lodash
```

---

## cb() never called

```
npm ERR! cb() never called!
npm ERR! This is an error with npm itself.
```

### What it means
npm crashed or froze during installation.

### Quick fix
```bash
# Clear everything and start fresh
npm cache clean --force
rm -rf node_modules package-lock.json
npm install
```

### If it keeps happening
```bash
# Update npm
npm install -g npm@latest

# Or use npm ci instead
rm -rf node_modules
npm ci
```

---

## Maximum call stack size exceeded

```
RangeError: Maximum call stack size exceeded
```

### What it means
Circular dependency or recursion issue.

### Quick fix
```bash
# Clean install
rm -rf node_modules package-lock.json
npm install

# If during build, check for circular imports in your code
```

### Find circular dependencies
```bash
# Use a tool to detect circular deps
npx madge --circular ./src
```

---

## Quick Reference

| Error | Likely Cause | Quick Fix |
|-------|--------------|-----------|
| EACCES | Permission issue | `sudo chown -R $(whoami) ~/.npm` |
| ENOENT | File not found | Check directory, reinstall |
| ERESOLVE | Dependency conflict | `npm install --legacy-peer-deps` |
| EINTEGRITY | Corrupted cache | `npm cache clean --force` |
| ENOTFOUND | Network/DNS issue | Check internet, registry URL |
| ERR_SOCKET_TIMEOUT | Slow network | Increase timeout |
| EPERM | File in use | Close apps, delete node_modules |
| ELIFECYCLE | Script error | Read the actual error above |
| peer dep missing | Missing package | Install the peer dep |
| Cannot find module | Not installed | `npm install package` |
| cb() never called | npm crashed | Clear cache, reinstall |

---

## Nuclear Option

When nothing works:

```bash
# Delete everything npm-related
rm -rf node_modules
rm -rf package-lock.json
rm -rf ~/.npm

# Clear cache
npm cache clean --force

# Reinstall
npm install
```

