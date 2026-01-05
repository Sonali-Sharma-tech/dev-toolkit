# Regex

> "I need to match..." → Here's the pattern + test strings

Regex patterns you'll actually copy-paste. Every pattern includes test strings so you can verify it works.

## Sections

| Guide | What's Inside |
|-------|---------------|
| [Basics](basics.md) | Syntax cheat sheet, metacharacters, quantifiers |
| [Recipes](recipes.md) | Copy-paste patterns with test strings |
| [By Language](by-language.md) | JS vs Python vs grep differences |
| [Patterns Reference](patterns.md) | Comprehensive pattern library |

---

## Quick Test

Every pattern in this guide includes test strings:

```
Pattern: \b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b

Matches:
  user@example.com
  first.last@company.co.uk
  name+tag@gmail.com

Doesn't match:
  @missing-username.com
  no-at-sign.com
  spaces in@email.com
```

Copy the pattern. Test against the strings. Verify it works.

---

## Testing Tools

| Tool | Best For |
|------|----------|
| [regex101.com](https://regex101.com) | Visual debugging with explanations |
| [regexr.com](https://regexr.com) | Learning with community patterns |
| `grep -E` | Command line testing |
| Browser console | Quick JavaScript tests |

---

## 30-Second Cheat Sheet

```
.       Any character (except newline)
*       0 or more
+       1 or more
?       0 or 1
^       Start of string
$       End of string
\d      Digit [0-9]
\w      Word char [a-zA-Z0-9_]
\s      Whitespace
[abc]   Character class
(x|y)   Alternation
\b      Word boundary
```

---

*Need a specific pattern? Check [Recipes](recipes.md) first.*
