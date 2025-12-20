# Contributing to Dev Toolkit

First off, thanks for taking the time to contribute! This project thrives on community contributions.

---

## Ways to Contribute

### 1. Add a New Command or One-Liner
Found a useful command that saved you time? Add it!

### 2. Fix Errors or Typos
Spotted a mistake? PRs welcome.

### 3. Add Platform-Specific Notes
Know how a command differs on macOS vs Linux? Add a note.

### 4. Improve Explanations
If something isn't clear, help make it better.

### 5. Suggest New Sections
Have an idea for a new topic? Open an issue to discuss.

---

## Content Guidelines

### Style
- **Copy-paste ready** - Minimize placeholders, maximize usability
- **Include "when to use"** - Context matters more than syntax
- **Real-world examples** - Practical over theoretical
- **Note platform differences** - macOS/Linux/Windows where applicable
- **No fluff** - Keep explanations concise

### Format
Every command entry should follow this structure:

```markdown
### Command Name

Brief description of what it does and when to use it.

```bash
command --flags arguments
```

**What it does:** One-line explanation.
```

### Code Blocks
- Always specify the language (```bash, ```json, etc.)
- Use comments inside code blocks for inline explanations
- Test commands before submitting

---

## How to Submit Changes

### Quick Edits
1. Click the pencil icon on any file in GitHub
2. Make your changes
3. Submit a pull request

### Larger Contributions
1. Fork the repository
2. Create a feature branch: `git checkout -b add-new-command`
3. Make your changes
4. Commit with a clear message: `git commit -m "Add: docker exec examples"`
5. Push to your fork: `git push origin add-new-command`
6. Open a pull request

---

## Commit Message Format

Use clear, descriptive commit messages:

```
Add: new command or section
Fix: error correction
Update: improvement to existing content
Remove: deprecated or incorrect content
```

Examples:
- `Add: kubectl port-forward examples`
- `Fix: typo in git rebase section`
- `Update: expand curl POST examples`

---

## Good First Issues

Look for issues labeled:
- `good first issue` - Great for newcomers
- `help wanted` - We need your expertise
- `documentation` - Content improvements

---

## Questions?

Open an issue or reach out. We're happy to help!

---

**Thank you for making dev-toolkit better for everyone!**
