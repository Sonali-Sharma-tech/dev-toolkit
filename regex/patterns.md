# Regex Patterns Reference

Real-world regex patterns you'll actually use. Copy-paste ready with explanations.

## Table of Contents
- [Quick Reference](#quick-reference)
- [Basic Patterns](#basic-patterns)
- [Email & URLs](#email--urls)
- [Phone Numbers](#phone-numbers)
- [Dates & Times](#dates--times)
- [Numbers & Currency](#numbers--currency)
- [Files & Paths](#files--paths)
- [Code & Programming](#code--programming)
- [Text Processing](#text-processing)
- [Validation Patterns](#validation-patterns)
- [Security & Sanitization](#security--sanitization)
- [Language Specific](#language-specific)
- [Testing & Debugging](#testing--debugging)

---

## Quick Reference

### Metacharacters
| Character | Meaning | Example |
|-----------|---------|---------|
| `.` | Any character (except newline) | `a.c` matches "abc", "a1c" |
| `*` | 0 or more | `ab*c` matches "ac", "abc", "abbc" |
| `+` | 1 or more | `ab+c` matches "abc", "abbc" (not "ac") |
| `?` | 0 or 1 | `ab?c` matches "ac", "abc" (not "abbc") |
| `^` | Start of string | `^Hello` matches "Hello world" |
| `$` | End of string | `world$` matches "Hello world" |
| `\` | Escape character | `\.` matches literal "." |
| `[]` | Character class | `[abc]` matches "a", "b", or "c" |
| `()` | Group | `(ab)+` matches "ab", "abab" |
| `|` | Alternation (OR) | `cat|dog` matches "cat" or "dog" |

### Character Classes
| Pattern | Meaning | Equivalent |
|---------|---------|------------|
| `\d` | Digit | `[0-9]` |
| `\D` | Non-digit | `[^0-9]` |
| `\w` | Word character | `[a-zA-Z0-9_]` |
| `\W` | Non-word | `[^a-zA-Z0-9_]` |
| `\s` | Whitespace | `[ \t\n\r\f]` |
| `\S` | Non-whitespace | `[^ \t\n\r\f]` |

### Quantifiers
| Pattern | Meaning |
|---------|---------|
| `{3}` | Exactly 3 |
| `{3,}` | 3 or more |
| `{3,5}` | Between 3 and 5 |
| `*?` | 0 or more (lazy) |
| `+?` | 1 or more (lazy) |

---

## Basic Patterns

### Match whole word
```regex
\bword\b
```
Use `\b` for word boundaries. Matches "word" but not "wordpress".

### Case insensitive match
```regex
(?i)pattern
/pattern/i                       # JavaScript, Perl
```

### Match any of multiple words
```regex
\b(cat|dog|bird)\b
```

### Match everything except a pattern
```regex
^(?!.*forbidden).*$              # Negative lookahead
```
Matches lines that don't contain "forbidden".

### Match if contains all words (any order)
```regex
^(?=.*word1)(?=.*word2)(?=.*word3).*$
```
Matches strings containing all three words in any order.

---

## Email & URLs

### Email (simple)
```regex
[\w.-]+@[\w.-]+\.\w+
```
Basic email validation. Matches: user@example.com

### Email (RFC compliant)
```regex
[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*
```

### URL (HTTP/HTTPS)
```regex
https?://[^\s]+
```
Simple URL match.

### URL (comprehensive)
```regex
https?:\/\/(www\.)?[-a-zA-Z0-9@:%._\+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b([-a-zA-Z0-9()@:%_\+.~#?&//=]*)
```

### Domain name
```regex
(?:[a-z0-9](?:[a-z0-9-]{0,61}[a-z0-9])?\.)+[a-z0-9](?:[a-z0-9-]{0,61}[a-z0-9])?
```

### URL slug
```regex
^[a-z0-9]+(?:-[a-z0-9]+)*$
```
Matches: "my-awesome-post", "product-123"

### YouTube video ID
```regex
(?:youtube\.com\/watch\?v=|youtu\.be\/)([a-zA-Z0-9_-]{11})
```
Extracts video ID from YouTube URLs.

---

## Phone Numbers

### US phone number
```regex
\(?\d{3}\)?[-.\s]?\d{3}[-.\s]?\d{4}
```
Matches: (123) 456-7890, 123-456-7890, 123.456.7890

### International phone
```regex
\+?[1-9]\d{1,14}
```
E.164 format: +1234567890

### Phone with country code
```regex
\+?\d{1,3}[-.\s]?\(?\d{1,3}\)?[-.\s]?\d{1,4}[-.\s]?\d{1,4}[-.\s]?\d{1,9}
```

### Indian mobile number
```regex
^[6-9]\d{9}$
```
10 digits starting with 6-9.

### UK phone number
```regex
^(?:(?:\+44)|(?:0))(?:\d{10}|\d{3}\s\d{3}\s\d{4})$
```

---

## Dates & Times

### Date (MM/DD/YYYY or MM-DD-YYYY)
```regex
(0[1-9]|1[0-2])[\/\-](0[1-9]|[12]\d|3[01])[\/\-](19|20)\d{2}
```

### Date (YYYY-MM-DD)
```regex
(19|20)\d{2}-(0[1-9]|1[0-2])-(0[1-9]|[12]\d|3[01])
```
ISO format.

### Date (DD/MM/YYYY)
```regex
(0[1-9]|[12]\d|3[01])[\/\-](0[1-9]|1[0-2])[\/\-](19|20)\d{2}
```

### Time (12-hour with optional AM/PM)
```regex
(1[0-2]|0?[1-9]):([0-5]\d)(\s?[AaPp][Mm])?
```

### Time (24-hour)
```regex
([01]?\d|2[0-3]):([0-5]\d)
```

### DateTime ISO 8601
```regex
\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}(\.\d{3})?Z?
```
Matches: 2023-11-27T14:30:00Z

### Relative time (e.g., "5 days ago")
```regex
\d+\s+(seconds?|minutes?|hours?|days?|weeks?|months?|years?)\s+ago
```

---

## Numbers & Currency

### Integer (positive/negative)
```regex
[+-]?\d+
```

### Decimal number
```regex
[+-]?\d+(\.\d+)?
```

### Percentage
```regex
\d+(\.\d+)?%
```

### Currency (USD)
```regex
\$\d{1,3}(,\d{3})*(\.\d{2})?
```
Matches: $1,234.56

### Currency (flexible)
```regex
[$€£¥]\s?\d{1,3}(,\d{3})*(\.\d{2})?
```

### Credit card number
```regex
\b(?:\d[ -]*?){13,16}\b
```
Basic format, spaces/dashes allowed.

### Visa card
```regex
4\d{3}[\s-]?\d{4}[\s-]?\d{4}[\s-]?\d{4}
```

### Mastercard
```regex
5[1-5]\d{2}[\s-]?\d{4}[\s-]?\d{4}[\s-]?\d{4}
```

### Thousand separator
```regex
\B(?=(\d{3})+(?!\d))
```
Use with replace to add commas: 1234567 → 1,234,567

---

## Files & Paths

### Filename with extension
```regex
[\w,\s-]+\.[A-Za-z]{2,4}
```

### Image files
```regex
\.(jpg|jpeg|png|gif|bmp|svg|webp)$
```
Case insensitive: `(?i)\.(jpg|jpeg|png|gif|bmp|svg|webp)$`

### JavaScript/TypeScript files
```regex
\.(jsx?|tsx?)$
```

### Path (Unix/Linux)
```regex
^(/[^/]+)+/?$
```

### Path (Windows)
```regex
^[a-zA-Z]:\\[\\\S|*\S]?.*$
```

### File path with line number
```regex
(.+):(\d+)(?::(\d+))?
```
Matches: "file.js:10:5" or "file.py:42"

### Git file status
```regex
^\s*([MADRCU?!])\s+(.+)$
```
Matches git status output.

---

## Code & Programming

### Variable name (most languages)
```regex
[a-zA-Z_]\w*
```

### Function call
```regex
(\w+)\s*\(([^)]*)\)
```
Captures function name and arguments.

### Import/require statements
```regex
(?:import|require)\s*\(?["']([^"']+)["']\)?
```

### HTML tag
```regex
<([a-z]+)([^<]+)*(?:>(.*)<\/\1>|\s+\/>)
```

### HTML tag with attributes
```regex
<(\w+)(\s+\w+(?:\s*=\s*(?:"[^"]*"|'[^']*'|[^>\s]+))?)*\s*\/?>
```

### CSS class names
```regex
\.([a-zA-Z_][\w-]*)
```

### Hex color code
```regex
#(?:[0-9a-fA-F]{3}){1,2}
```
Matches: #fff, #ffffff

### RGB color
```regex
rgb\(\s*\d{1,3}\s*,\s*\d{1,3}\s*,\s*\d{1,3}\s*\)
```

### JavaScript comments
```regex
\/\*[\s\S]*?\*\/|\/\/.*$
```
Matches both /* */ and // comments.

### Python docstring
```regex
"""[\s\S]*?"""|'''[\s\S]*?'''
```

### Markdown header
```regex
^#{1,6}\s+.*$
```

### Git commit hash
```regex
\b[0-9a-f]{7,40}\b
```

### Semantic version
```regex
^v?\d+\.\d+\.\d+(?:-[\w.]+)?(?:\+[\w.]+)?$
```
Matches: 1.0.0, v2.1.3-beta.1

---

## Text Processing

### Remove extra whitespace
```regex
\s+
```
Replace with single space.

### Trim whitespace
```regex
^\s+|\s+$
```
Remove leading/trailing spaces.

### Empty lines
```regex
^\s*$
```

### Lines with specific word
```regex
^.*\bword\b.*$
```

### Extract text between quotes
```regex
["']([^"']+)["']
```
Captures content between quotes.

### Extract text between parentheses
```regex
\(([^)]+)\)
```

### Extract text between brackets
```regex
\[([^\]]+)\]
```

### Sentence detection
```regex
[.!?]+\s*
```
Split text into sentences.

### Word wrap at N characters
```regex
.{1,80}(?:\s|$)
```
Wrap at 80 characters on word boundary.

### CamelCase to snake_case
```regex
([a-z])([A-Z])
```
Replace with: `$1_$2` then lowercase.

### Title case
```regex
\b\w
```
Match first letter of each word to capitalize.

---

## Validation Patterns

### Password (strong)
```regex
^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$
```
Min 8 chars, uppercase, lowercase, number, special char.

### Username
```regex
^[a-zA-Z0-9_]{3,16}$
```
3-16 characters, alphanumeric and underscore.

### Alphanumeric only
```regex
^[a-zA-Z0-9]+$
```

### Letters only
```regex
^[a-zA-Z]+$
```

### No special characters
```regex
^[a-zA-Z0-9\s]+$
```

### ZIP code (US)
```regex
^\d{5}(?:-\d{4})?$
```
Matches: 12345 or 12345-6789

### ZIP code (UK)
```regex
[A-Z]{1,2}\d[A-Z\d]? ?\d[A-Z]{2}
```

### Social Security Number (SSN)
```regex
^\d{3}-?\d{2}-?\d{4}$
```

### UUID
```regex
^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$
```

### MAC address
```regex
^([0-9A-Fa-f]{2}[:-]){5}([0-9A-Fa-f]{2})$
```

---

## Security & Sanitization

### SQL injection attempt (basic)
```regex
('|(--|;)|(\*/)|(/\*))
```
Detect common SQL injection patterns.

### XSS attempt (basic)
```regex
<script[^>]*>[\s\S]*?</script>
```

### Remove HTML tags
```regex
<[^>]*>
```
Strip all HTML tags from text.

### Escape HTML entities
```regex
[&<>"']
```
Match characters that need HTML escaping.

### Command injection attempt
```regex
[;&|`]|\$\(|\${
```
Detect shell command separators.

### Path traversal attempt
```regex
\.\.\/|\.\.\\
```
Detect directory traversal patterns.

### Sensitive data patterns
```regex
# API keys (generic)
[a-zA-Z0-9_-]{32,}

# AWS Access Key
AKIA[0-9A-Z]{16}

# Private key header
-----BEGIN\s+(?:RSA\s+)?PRIVATE\s+KEY-----
```

---

## Language Specific

### JavaScript/TypeScript

#### Arrow function
```regex
\(([^)]*)\)\s*=>\s*
```

#### Async function
```regex
async\s+(?:function\s+)?(\w+)?\s*\([^)]*\)
```

#### Import destructuring
```regex
import\s*{\s*([^}]+)\s*}\s*from\s*['"]([^'"]+)['"]
```

### Python

#### Function definition
```regex
def\s+(\w+)\s*\(([^)]*)\)\s*:
```

#### Class definition
```regex
class\s+(\w+)(?:\(([^)]*)\))?\s*:
```

#### Decorator
```regex
@(\w+)(?:\(([^)]*)\))?
```

### SQL

#### SELECT statement
```regex
SELECT\s+(.+?)\s+FROM\s+(\w+)
```

#### Table name extraction
```regex
(?:FROM|JOIN|INTO|UPDATE|TABLE)\s+`?(\w+)`?
```

### Markdown

#### Link
```regex
\[([^\]]+)\]\(([^)]+)\)
```

#### Image
```regex
!\[([^\]]*)\]\(([^)]+)\)
```

#### Code block
```regex
```(\w*)\n([\s\S]*?)\n```
```

---

## Testing & Debugging

### Test at regex101.com
Best online regex tester with explanations.

### Common debugging patterns

#### Debug print statements
```regex
console\.log\(|print\(|println\(|printf\(
```

#### TODO comments
```regex
(?:\/\/|#|\/\*)\s*(?:TODO|FIXME|HACK|XXX|BUG):?\s*(.*)
```

#### Commented code
```regex
^\s*\/\/.*$|^\s*#.*$
```

### Performance tips
1. Use non-capturing groups when you don't need the match: `(?:...)`
2. Be specific: `[0-9]` is faster than `\d` in some engines
3. Avoid catastrophic backtracking: `(a+)+b` is bad
4. Use anchors when possible: `^...$`
5. Lazy quantifiers for better performance: `.*?` instead of `.*`

### Regex flags
| Flag | Meaning | Example |
|------|---------|---------|
| `g` | Global (all matches) | `/pattern/g` |
| `i` | Case insensitive | `/pattern/i` |
| `m` | Multiline mode | `/^pattern/m` |
| `s` | Dot matches newline | `/pattern/s` |
| `u` | Unicode mode | `/pattern/u` |
| `x` | Verbose (ignore whitespace) | `/pattern/x` |

---

## Quick Testing Commands

### grep (Linux/macOS)
```bash
# Basic search
grep -E 'pattern' file.txt

# Case insensitive
grep -i 'pattern' file.txt

# Show only matched part
grep -o 'pattern' file.txt

# Count matches
grep -c 'pattern' file.txt
```

### JavaScript
```javascript
// Test
/pattern/.test('string')

// Match
'string'.match(/pattern/g)

// Replace
'string'.replace(/pattern/g, 'replacement')

// Split
'string'.split(/pattern/)
```

### Python
```python
import re

# Search
re.search(r'pattern', 'string')

# Find all
re.findall(r'pattern', 'string')

# Replace
re.sub(r'pattern', 'replacement', 'string')

# Compile for performance
pattern = re.compile(r'pattern')
pattern.findall('string')
```

---

*Pro tip: When in doubt, test on [regex101.com](https://regex101.com) - it explains each part of your pattern and shows live matches.*