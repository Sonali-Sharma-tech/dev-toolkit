# Yarn & pnpm

> npm alternatives. When and why to switch.

## Table of Contents
- [Quick Comparison](#quick-comparison)
- [Yarn Commands](#yarn-commands)
- [pnpm Commands](#pnpm-commands)
- [When to Use Which](#when-to-use-which)
- [Migration](#migration)

---

## Quick Comparison

| Feature | npm | Yarn | pnpm |
|---------|-----|------|------|
| Speed | Baseline | Faster | Fastest |
| Disk space | High | High | Low (shared) |
| Lock file | package-lock.json | yarn.lock | pnpm-lock.yaml |
| Workspaces | Yes | Yes | Yes |
| Plug'n'Play | No | Yes (v2+) | No |
| Default in Node | Yes | No | No |

### When to Choose Each

- **npm**: Default, works everywhere, good enough for most projects
- **Yarn**: Better monorepo support, deterministic installs
- **pnpm**: Monorepos, disk space savings, strictest dependency resolution

---

## Yarn Commands

### Installation

```bash
npm install -g yarn
```

### Basic Commands

| npm | Yarn |
|-----|------|
| `npm install` | `yarn` or `yarn install` |
| `npm install pkg` | `yarn add pkg` |
| `npm install -D pkg` | `yarn add -D pkg` |
| `npm install -g pkg` | `yarn global add pkg` |
| `npm uninstall pkg` | `yarn remove pkg` |
| `npm update` | `yarn upgrade` |
| `npm run script` | `yarn script` |
| `npm init` | `yarn init` |
| `npm ci` | `yarn install --frozen-lockfile` |

### Yarn Specific

```bash
# Interactive upgrade
yarn upgrade-interactive

# Why is this package installed?
yarn why lodash

# Check for outdated
yarn outdated

# Clean cache
yarn cache clean
```

### Workspaces (Monorepo)

**package.json:**
```json
{
  "private": true,
  "workspaces": ["packages/*"]
}
```

```bash
# Install all workspace deps
yarn install

# Run script in specific workspace
yarn workspace @myorg/app build

# Add dep to specific workspace
yarn workspace @myorg/app add lodash

# Run script in all workspaces
yarn workspaces run build
```

---

## pnpm Commands

### Installation

```bash
npm install -g pnpm
```

### Basic Commands

| npm | pnpm |
|-----|------|
| `npm install` | `pnpm install` |
| `npm install pkg` | `pnpm add pkg` |
| `npm install -D pkg` | `pnpm add -D pkg` |
| `npm install -g pkg` | `pnpm add -g pkg` |
| `npm uninstall pkg` | `pnpm remove pkg` |
| `npm update` | `pnpm update` |
| `npm run script` | `pnpm script` |
| `npm init` | `pnpm init` |
| `npm ci` | `pnpm install --frozen-lockfile` |

### pnpm Specific

```bash
# Why is this package installed?
pnpm why lodash

# Check for outdated
pnpm outdated

# Prune unused packages
pnpm prune

# Store status (disk savings)
pnpm store status

# Clean store
pnpm store prune
```

### Workspaces (Monorepo)

**pnpm-workspace.yaml:**
```yaml
packages:
  - 'packages/*'
```

```bash
# Install all workspace deps
pnpm install

# Run script in specific workspace
pnpm --filter @myorg/app build

# Add dep to specific workspace
pnpm --filter @myorg/app add lodash

# Run script in all workspaces
pnpm -r run build

# Run only in changed packages
pnpm -r --filter '...[origin/main]' run test
```

---

## When to Use Which

### Use npm When:

- Starting out / learning
- Simple projects
- Maximum compatibility needed
- Team unfamiliar with alternatives

### Use Yarn When:

- Need Plug'n'Play (zero node_modules)
- Better monorepo tooling needed
- Want interactive upgrades
- Team already uses it

### Use pnpm When:

- Disk space is a concern
- Many projects sharing dependencies
- Strict dependency resolution needed
- Large monorepos
- CI speed is critical

---

## Migration

### npm → Yarn

```bash
rm package-lock.json
rm -rf node_modules
yarn install
```

### npm → pnpm

```bash
rm package-lock.json
rm -rf node_modules
pnpm import    # Optional: converts npm lock
pnpm install
```

### Yarn → pnpm

```bash
rm yarn.lock
rm -rf node_modules
pnpm import    # Optional: converts yarn lock
pnpm install
```

### Keep Both Lock Files (Not Recommended)

Add to `.npmrc`:
```
package-lock=false
```

---

## Command Cheat Sheet

| Task | npm | Yarn | pnpm |
|------|-----|------|------|
| Install all | `npm install` | `yarn` | `pnpm install` |
| Add package | `npm i pkg` | `yarn add pkg` | `pnpm add pkg` |
| Add dev dep | `npm i -D pkg` | `yarn add -D pkg` | `pnpm add -D pkg` |
| Remove | `npm uninstall pkg` | `yarn remove pkg` | `pnpm remove pkg` |
| Update all | `npm update` | `yarn upgrade` | `pnpm update` |
| Run script | `npm run dev` | `yarn dev` | `pnpm dev` |
| Global add | `npm i -g pkg` | `yarn global add pkg` | `pnpm add -g pkg` |
| Clean install | `npm ci` | `yarn --frozen-lockfile` | `pnpm i --frozen-lockfile` |
| Why installed | `npm explain pkg` | `yarn why pkg` | `pnpm why pkg` |
| Outdated | `npm outdated` | `yarn outdated` | `pnpm outdated` |

---

## Speed Comparison

Typical fresh install times (varies by project):

| Package Manager | Cold Cache | Warm Cache |
|-----------------|------------|------------|
| npm | 60s | 30s |
| Yarn | 45s | 15s |
| pnpm | 35s | 10s |

pnpm is fastest because it links from a global store instead of copying.

---

## Disk Usage Comparison

For a project with 500MB of dependencies, installed across 5 projects:

| Package Manager | Disk Used |
|-----------------|-----------|
| npm | 2.5 GB (500MB × 5) |
| Yarn | 2.5 GB (500MB × 5) |
| pnpm | ~600 MB (shared store) |

pnpm saves disk because all projects share the same package store.

---

*Start with npm. Switch to pnpm for monorepos or disk savings.*
