# Scripts

> Actual automation scripts that solve real problems.

Not tutorials. Working code you can use today.

## Available Scripts

| Script | What it solves |
|--------|----------------|
| [Auto-Update macOS](auto-update-macos.md) | Keep all dev tools updated automatically |
| [Dev Environment Setup](dev-environment.md) | New Mac → coding in 30 minutes |
| [Git Hooks](git-hooks.md) | Catch problems before commits/pushes |

---

## Philosophy

Each script includes:
- **The problem**: Why this script exists
- **The solution**: Complete, working code
- **Customization**: How to adapt it
- **Gotchas**: What can go wrong

---

## Quick Links

### New Mac Setup
```bash
curl -fsSL https://raw.githubusercontent.com/YOUR_USERNAME/dev-toolkit/main/scripts/setup.sh | bash
```

### Auto-Update Everything
```bash
brew autoupdate start 86400 --upgrade --cleanup --immediate
```

### Install Git Hooks (Husky)
```bash
npm install --save-dev husky && npx husky init
```
