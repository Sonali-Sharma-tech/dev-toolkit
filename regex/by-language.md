# Regex by Language

> Same pattern, different syntax. Here's how to translate.

The same regex pattern works differently (or not at all) across languages. This guide shows the differences.

## Table of Contents
- [Quick Comparison](#quick-comparison)
- [JavaScript](#javascript)
- [Python](#python)
- [Bash / grep / sed](#bash--grep--sed)
- [Go](#go)
- [Common Gotchas](#common-gotchas)
- [Cheat Sheet](#cheat-sheet)

---

## Quick Comparison

### Same Pattern, Different Syntax

Match an email address:

```javascript
// JavaScript
const email = /[\w.+-]+@[\w-]+\.[\w.-]+/;
email.test("user@example.com");  // true
```

```python
# Python
import re
email = r'[\w.+-]+@[\w-]+\.[\w.-]+'
re.search(email, "user@example.com")  # Match object
```

```bash
# grep
echo "user@example.com" | grep -E '[\w.+-]+@[\w-]+\.[\w.-]+'

# Note: \w might not work in all grep versions
echo "user@example.com" | grep -E '[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9.-]+'
```

```go
// Go
import "regexp"
email := regexp.MustCompile(`[\w.+-]+@[\w-]+\.[\w.-]+`)
email.MatchString("user@example.com")  // true
```

---

## JavaScript

### Creating Regex

```javascript
// Literal syntax (preferred)
const pattern = /hello/i;

// Constructor (for dynamic patterns)
const pattern = new RegExp('hello', 'i');
const dynamic = new RegExp(userInput.replace(/[.*+?^${}()|[\]\\]/g, '\\$&'));
```

### Common Methods

```javascript
// Test if matches
/pattern/.test('string')              // true/false

// Find first match
'string'.match(/pattern/)             // ['match'] or null

// Find all matches
'string'.match(/pattern/g)            // ['match1', 'match2'] or null

// Find with details
'string'.matchAll(/pattern/g)         // Iterator of match objects

// Replace
'string'.replace(/pattern/, 'new')    // First only
'string'.replace(/pattern/g, 'new')   // All

// Split
'string'.split(/pattern/)             // Array
```

### Capture Groups

```javascript
const result = 'hello world'.match(/(\w+) (\w+)/);
// result[0] = 'hello world' (full match)
// result[1] = 'hello'        (group 1)
// result[2] = 'world'        (group 2)

// Named groups
const result = 'hello world'.match(/(?<first>\w+) (?<second>\w+)/);
result.groups.first   // 'hello'
result.groups.second  // 'world'
```

### Replace with Captured Groups

```javascript
// Using $1, $2 for groups
'hello world'.replace(/(\w+) (\w+)/, '$2 $1')
// 'world hello'

// Using function
'hello world'.replace(/\w+/g, match => match.toUpperCase())
// 'HELLO WORLD'

// Named group in replacement
'hello world'.replace(/(?<word>\w+)/g, '[$<word>]')
// '[hello] [world]'
```

### Flags

```javascript
/pattern/i    // Case insensitive
/pattern/g    // Global (all matches)
/pattern/m    // Multiline (^ and $ match line boundaries)
/pattern/s    // Dotall (. matches newlines)
/pattern/u    // Unicode
/pattern/y    // Sticky (match at lastIndex only)

// Combine flags
/pattern/gi   // Global and case insensitive
```

### Example: Validate and Extract

```javascript
function parseURL(url) {
  const pattern = /^(https?):\/\/([^\/]+)(\/.*)?$/;
  const match = url.match(pattern);

  if (!match) return null;

  return {
    protocol: match[1],
    host: match[2],
    path: match[3] || '/'
  };
}

parseURL('https://example.com/page');
// { protocol: 'https', host: 'example.com', path: '/page' }
```

---

## Python

### Creating Regex

```python
import re

# Raw strings (preferred - avoids escaping issues)
pattern = r'\d+\.\d+'

# Compile for reuse
pattern = re.compile(r'\d+\.\d+')
```

### Common Methods

```python
import re

# Search (first match anywhere)
re.search(r'pattern', 'string')       # Match object or None

# Match (from start only)
re.match(r'pattern', 'string')        # Match object or None

# Full match
re.fullmatch(r'pattern', 'string')    # Match object or None

# Find all
re.findall(r'pattern', 'string')      # List of strings
re.finditer(r'pattern', 'string')     # Iterator of Match objects

# Replace
re.sub(r'pattern', 'replacement', 'string')
re.sub(r'pattern', 'replacement', 'string', count=1)  # First only

# Split
re.split(r'pattern', 'string')        # List
```

### Capture Groups

```python
import re

match = re.search(r'(\w+) (\w+)', 'hello world')

match.group(0)    # 'hello world' (full match)
match.group(1)    # 'hello'
match.group(2)    # 'world'
match.groups()    # ('hello', 'world')

# Named groups
match = re.search(r'(?P<first>\w+) (?P<second>\w+)', 'hello world')
match.group('first')   # 'hello'
match.group('second')  # 'world'
match.groupdict()      # {'first': 'hello', 'second': 'world'}
```

### Replace with Captured Groups

```python
import re

# Using \1, \2 for groups
re.sub(r'(\w+) (\w+)', r'\2 \1', 'hello world')
# 'world hello'

# Using function
re.sub(r'\w+', lambda m: m.group().upper(), 'hello world')
# 'HELLO WORLD'

# Named groups
re.sub(r'(?P<word>\w+)', r'[\g<word>]', 'hello world')
# '[hello] [world]'
```

### Flags

```python
import re

re.IGNORECASE  # or re.I - Case insensitive
re.MULTILINE   # or re.M - ^ and $ match line boundaries
re.DOTALL      # or re.S - . matches newlines
re.VERBOSE     # or re.X - Allow comments and whitespace
re.ASCII       # or re.A - ASCII-only matching

# Using flags
re.search(r'pattern', 'string', re.IGNORECASE)
re.search(r'pattern', 'string', re.I | re.M)  # Combine flags

# In pattern
re.search(r'(?i)pattern', 'string')  # Inline flag
```

### Example: Validate and Extract

```python
import re

def parse_url(url):
    pattern = r'^(https?):\/\/([^\/]+)(\/.*)?$'
    match = re.search(pattern, url)

    if not match:
        return None

    return {
        'protocol': match.group(1),
        'host': match.group(2),
        'path': match.group(3) or '/'
    }

parse_url('https://example.com/page')
# {'protocol': 'https', 'host': 'example.com', 'path': '/page'}
```

### Verbose Mode (Readable Regex)

```python
import re

pattern = re.compile(r'''
    ^                   # Start of string
    (?P<protocol>https?)  # Protocol
    ://                 # Separator
    (?P<host>[^/]+)     # Host
    (?P<path>/.*)?      # Optional path
    $                   # End of string
''', re.VERBOSE)

match = pattern.search('https://example.com/page')
match.groupdict()
# {'protocol': 'https', 'host': 'example.com', 'path': '/page'}
```

---

## Bash / grep / sed

### grep Flavors

```bash
# Basic regex (BRE) - default
grep 'pattern' file

# Extended regex (ERE) - more features
grep -E 'pattern' file
# or
egrep 'pattern' file

# Perl regex (PCRE) - full features
grep -P 'pattern' file
```

### BRE vs ERE Differences

| Feature | BRE | ERE |
|---------|-----|-----|
| `+` one or more | `\+` | `+` |
| `?` zero or one | `\?` | `?` |
| `|` alternation | `\|` | `|` |
| `{}` quantifier | `\{n\}` | `{n}` |
| `()` grouping | `\(\)` | `()` |

```bash
# BRE (escape special chars)
grep 'hello\+' file

# ERE (no escaping)
grep -E 'hello+' file
```

### Common grep Options

```bash
grep -i 'pattern' file    # Case insensitive
grep -v 'pattern' file    # Invert (lines NOT matching)
grep -c 'pattern' file    # Count matches
grep -l 'pattern' *       # List files with matches
grep -n 'pattern' file    # Show line numbers
grep -o 'pattern' file    # Show only matched part
grep -w 'word' file       # Match whole word only
grep -r 'pattern' dir/    # Recursive
```

### sed Substitution

```bash
# Replace first match per line
sed 's/old/new/' file

# Replace all matches per line
sed 's/old/new/g' file

# Case insensitive
sed 's/old/new/gi' file

# In-place edit
sed -i 's/old/new/g' file           # Linux
sed -i '' 's/old/new/g' file        # macOS

# Using different delimiter
sed 's|/path/old|/path/new|g' file  # Useful for paths
```

### sed with Capture Groups

```bash
# Swap words (note: \1 \2 for groups)
echo "hello world" | sed 's/\(\w\+\) \(\w\+\)/\2 \1/'
# world hello

# With ERE
echo "hello world" | sed -E 's/(\w+) (\w+)/\2 \1/'
# world hello
```

### awk Regex

```bash
# Match pattern
awk '/pattern/ { print }' file

# Match in specific field
awk '$1 ~ /pattern/ { print }' file

# Replace
awk '{ gsub(/old/, "new"); print }' file
```

### Real Examples

```bash
# Find all TODOs in code
grep -rn 'TODO:' --include='*.js' src/

# Extract email addresses
grep -oE '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' file

# Remove HTML tags
sed 's/<[^>]*>//g' file.html

# Convert tabs to spaces
sed 's/\t/    /g' file

# Delete empty lines
sed '/^$/d' file

# Extract URLs
grep -oP 'https?://[^\s]+' file
```

---

## Go

### Creating Regex

```go
import "regexp"

// MustCompile panics on invalid pattern (use for constants)
pattern := regexp.MustCompile(`\d+`)

// Compile returns error (use for user input)
pattern, err := regexp.Compile(`\d+`)
if err != nil {
    // Handle invalid pattern
}
```

### Common Methods

```go
import "regexp"

re := regexp.MustCompile(`\d+`)

// Test if matches
re.MatchString("abc123")           // true

// Find first match
re.FindString("abc123def456")      // "123"

// Find all matches
re.FindAllString("abc123def456", -1)  // ["123", "456"]
re.FindAllString("abc123def456", 1)   // ["123"] (limit)

// Replace
re.ReplaceAllString("abc123", "X")    // "abcX"

// Split
re.Split("a1b2c3", -1)                // ["a", "b", "c", ""]
```

### Capture Groups

```go
import "regexp"

re := regexp.MustCompile(`(\w+) (\w+)`)

// FindStringSubmatch - one match with groups
match := re.FindStringSubmatch("hello world")
// match[0] = "hello world"
// match[1] = "hello"
// match[2] = "world"

// FindAllStringSubmatch - all matches with groups
matches := re.FindAllStringSubmatch("hello world foo bar", -1)
// matches[0] = ["hello world", "hello", "world"]
// matches[1] = ["foo bar", "foo", "bar"]

// Named groups
re := regexp.MustCompile(`(?P<first>\w+) (?P<second>\w+)`)
names := re.SubexpNames()  // ["", "first", "second"]
```

### Replace with Captured Groups

```go
import "regexp"

re := regexp.MustCompile(`(\w+) (\w+)`)

// Using $1, $2
re.ReplaceAllString("hello world", "$2 $1")
// "world hello"

// Using function
re.ReplaceAllStringFunc("hello world", strings.ToUpper)
// "HELLO WORLD"
```

### Flags (Inline Only)

Go doesn't have separate flags - use inline:

```go
// Case insensitive
regexp.MustCompile(`(?i)hello`)

// Multiline
regexp.MustCompile(`(?m)^line`)

// Dot matches newline
regexp.MustCompile(`(?s).*`)

// Combine
regexp.MustCompile(`(?im)^hello`)
```

### Example: Validate and Extract

```go
import "regexp"

func parseURL(url string) map[string]string {
    re := regexp.MustCompile(`^(https?):\/\/([^\/]+)(\/.*)?$`)
    match := re.FindStringSubmatch(url)

    if match == nil {
        return nil
    }

    path := match[3]
    if path == "" {
        path = "/"
    }

    return map[string]string{
        "protocol": match[1],
        "host":     match[2],
        "path":     path,
    }
}
```

### Go Limitations

Go uses RE2, which doesn't support:
- Backreferences (`\1`)
- Lookahead/lookbehind (`(?=...)`, `(?<=...)`)
- Atomic groups
- Possessive quantifiers

If you need these, consider the `regexp2` package.

---

## Common Gotchas

### 1. Raw Strings

```python
# Python: Use r'' to avoid escaping backslashes
pattern = r'\d+'      # Correct
pattern = '\\d+'      # Also works but messy
```

```go
// Go: Use backticks
pattern := `\d+`      // Correct
pattern := "\\d+"     // Also works but messy
```

```javascript
// JavaScript: Literals don't need escaping
/\d+/                 // Correct
new RegExp('\\d+')    // Constructor needs escaping
```

### 2. Escaping in Different Contexts

```javascript
// String escaping vs regex escaping
const str = 'Hello\\nWorld';      // Literal \n
const regex = /\n/;               // Newline character
const regex2 = new RegExp('\\n'); // Newline character
```

### 3. `\w` and `\d` Differences

```python
# Python 3: \d matches Unicode digits by default
import re
re.findall(r'\d', '123٤٥٦')  # ['1', '2', '3', '٤', '٥', '٦']

# ASCII only
re.findall(r'\d', '123٤٥٦', re.ASCII)  # ['1', '2', '3']
```

```javascript
// JavaScript: \d is ASCII only
'123٤٥٦'.match(/\d/g)  // ['1', '2', '3']
```

### 4. Multiline Mode Differences

```javascript
// JavaScript
/^line/m.test('first\nline')  // true (^ matches after \n)

// Python
re.search(r'^line', 'first\nline', re.MULTILINE)  # Match
```

### 5. Global Flag Gotcha (JavaScript)

```javascript
// With /g, lastIndex moves - can cause issues
const re = /\d+/g;
re.test('123');  // true, lastIndex = 3
re.test('123');  // false! lastIndex was at 3
re.test('123');  // true, lastIndex reset to 0

// Solution: don't reuse with /g, or reset
re.lastIndex = 0;
```

### 6. grep Character Classes

```bash
# \w and \d may not work in basic grep
grep '\d' file       # Might not work

# Use POSIX classes instead
grep '[[:digit:]]' file

# Or use -P for Perl regex
grep -P '\d' file
```

---

## Cheat Sheet

### Creating Patterns

| Language | Literal | Compiled |
|----------|---------|----------|
| JavaScript | `/pattern/` | `new RegExp('pattern')` |
| Python | `r'pattern'` | `re.compile(r'pattern')` |
| Go | `` `pattern` `` | `regexp.MustCompile(`pattern`)` |
| Bash grep | `'pattern'` | N/A |

### Test if Matches

| Language | Code |
|----------|------|
| JavaScript | `/pattern/.test(str)` |
| Python | `re.search(r'pattern', str)` |
| Go | `re.MatchString(str)` |
| Bash | `grep -q 'pattern' <<< "$str"` |

### Find First Match

| Language | Code |
|----------|------|
| JavaScript | `str.match(/pattern/)` |
| Python | `re.search(r'pattern', str)` |
| Go | `re.FindString(str)` |
| Bash | `grep -o 'pattern' <<< "$str" \| head -1` |

### Find All Matches

| Language | Code |
|----------|------|
| JavaScript | `str.match(/pattern/g)` |
| Python | `re.findall(r'pattern', str)` |
| Go | `re.FindAllString(str, -1)` |
| Bash | `grep -o 'pattern' <<< "$str"` |

### Replace

| Language | Code |
|----------|------|
| JavaScript | `str.replace(/pattern/g, 'new')` |
| Python | `re.sub(r'pattern', 'new', str)` |
| Go | `re.ReplaceAllString(str, "new")` |
| Bash | `sed 's/pattern/new/g' <<< "$str"` |

### Group References in Replace

| Language | Syntax |
|----------|--------|
| JavaScript | `$1`, `$2` |
| Python | `\1`, `\2` or `\g<name>` |
| Go | `$1`, `$2` |
| sed (BRE) | `\1`, `\2` |
| sed (ERE) | `\1`, `\2` |

### Flags

| Flag | JS | Python | Go | grep |
|------|-----|--------|-----|------|
| Case insensitive | `i` | `re.I` | `(?i)` | `-i` |
| Global | `g` | N/A | N/A | N/A |
| Multiline | `m` | `re.M` | `(?m)` | N/A |
| Dotall | `s` | `re.S` | `(?s)` | N/A |

---

*When in doubt, test at [regex101.com](https://regex101.com) - it supports JavaScript, Python, and Go flavors.*
