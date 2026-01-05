# Regex Basics

> The building blocks. Learn these, understand any pattern.

## Table of Contents
- [Metacharacters](#metacharacters)
- [Character Classes](#character-classes)
- [Quantifiers](#quantifiers)
- [Anchors](#anchors)
- [Groups and Capturing](#groups-and-capturing)
- [Lookahead and Lookbehind](#lookahead-and-lookbehind)
- [Flags](#flags)
- [Common Gotchas](#common-gotchas)

---

## Metacharacters

The special characters that give regex its power.

| Character | Meaning | Example | Matches | Doesn't Match |
|-----------|---------|---------|---------|---------------|
| `.` | Any single character | `a.c` | abc, a1c, a-c | ac, abbc |
| `*` | 0 or more of previous | `ab*c` | ac, abc, abbc | adc |
| `+` | 1 or more of previous | `ab+c` | abc, abbc | ac |
| `?` | 0 or 1 of previous | `ab?c` | ac, abc | abbc |
| `\` | Escape special char | `a\.b` | a.b | aXb |
| `\|` | OR (alternation) | `cat\|dog` | cat, dog | cats |
| `[]` | Character class | `[abc]` | a, b, c | d |
| `()` | Group | `(ab)+` | ab, abab | a, b |
| `^` | Start of string | `^Hello` | "Hello world" | "Say Hello" |
| `$` | End of string | `end$` | "The end" | "endless" |

### Escaping Special Characters

These characters need `\` before them to match literally:

```
. * + ? ^ $ \ | [ ] ( ) { }
```

```
Pattern: \$\d+\.\d{2}

Matches:
  $19.99
  $100.00
  $0.50

Doesn't match:
  19.99
  $19
  $19.9
```

---

## Character Classes

Match any character from a set.

### Predefined Classes

| Pattern | Meaning | Equivalent |
|---------|---------|------------|
| `\d` | Any digit | `[0-9]` |
| `\D` | Non-digit | `[^0-9]` |
| `\w` | Word character | `[a-zA-Z0-9_]` |
| `\W` | Non-word | `[^a-zA-Z0-9_]` |
| `\s` | Whitespace | `[ \t\n\r\f]` |
| `\S` | Non-whitespace | `[^ \t\n\r\f]` |

### Custom Classes

```
[abc]       Match a, b, or c
[a-z]       Match any lowercase letter
[A-Z]       Match any uppercase letter
[0-9]       Match any digit
[a-zA-Z]    Match any letter
[^abc]      Match anything EXCEPT a, b, or c
[a-z0-9]    Match lowercase letter OR digit
```

```
Pattern: [A-Z][a-z]+

Matches:
  Hello
  World
  Python

Doesn't match:
  hello
  WORLD
  123
```

### Negation with `^`

Inside brackets, `^` means "not":

```
Pattern: [^aeiou]

Matches:
  b
  c
  1
  !

Doesn't match:
  a
  e
  i
  o
  u
```

---

## Quantifiers

Control how many times a pattern matches.

| Quantifier | Meaning | Example | Matches |
|------------|---------|---------|---------|
| `*` | 0 or more | `a*` | "", a, aa, aaa |
| `+` | 1 or more | `a+` | a, aa, aaa |
| `?` | 0 or 1 | `a?` | "", a |
| `{3}` | Exactly 3 | `a{3}` | aaa |
| `{2,4}` | 2 to 4 | `a{2,4}` | aa, aaa, aaaa |
| `{2,}` | 2 or more | `a{2,}` | aa, aaa, aaaa... |

### Greedy vs Lazy

By default, quantifiers are **greedy** (match as much as possible).

Add `?` to make them **lazy** (match as little as possible):

```
String: <div>content</div>

Greedy:  <.*>   matches: <div>content</div>
Lazy:    <.*?>  matches: <div>
```

| Greedy | Lazy |
|--------|------|
| `*` | `*?` |
| `+` | `+?` |
| `?` | `??` |
| `{n,m}` | `{n,m}?` |

```
Pattern: ".+?"

Test string: He said "hello" and "goodbye"

Matches:
  "hello"
  "goodbye"

With greedy ".+":
  "hello" and "goodbye"   (not what you want!)
```

---

## Anchors

Match positions, not characters.

| Anchor | Meaning |
|--------|---------|
| `^` | Start of string (or line with `m` flag) |
| `$` | End of string (or line with `m` flag) |
| `\b` | Word boundary |
| `\B` | Not word boundary |

### Word Boundaries

The `\b` anchor is incredibly useful:

```
Pattern: \bcat\b

Matches:
  "The cat sat"
  "cat"
  "my cat's toy"

Doesn't match:
  "category"
  "concatenate"
  "wildcat"
```

Word boundary = transition between `\w` and `\W` characters.

### Start and End

```
Pattern: ^Error

Matches:
  "Error: Something went wrong"
  "Error 404"

Doesn't match:
  "An Error occurred"
  "NoError"
```

```
Pattern: \.js$

Matches:
  "script.js"
  "app.min.js"

Doesn't match:
  "script.jsx"
  "js.bak"
```

---

## Groups and Capturing

### Basic Groups

Parentheses create groups:

```
Pattern: (ab)+

Matches:
  ab
  abab
  ababab

Doesn't match:
  a
  b
  aabb
```

### Capturing Groups

Groups capture matched text for later use:

```
Pattern: (\d{3})-(\d{4})

Test: "Call 555-1234"

Group 0 (full match): 555-1234
Group 1: 555
Group 2: 1234
```

### Named Groups

Give groups names for clarity:

```javascript
// JavaScript
/(?<area>\d{3})-(?<number>\d{4})/

// Python
r'(?P<area>\d{3})-(?P<number>\d{4})'
```

### Non-Capturing Groups

When you need grouping but not capturing:

```
Pattern: (?:ab)+c

Matches: abc, ababc
Same as (ab)+c but doesn't capture "ab"
```

Use `(?:...)` for better performance when you don't need the captured value.

### Backreferences

Refer to captured groups:

```
Pattern: (\w+)\s+\1

Matches (repeated words):
  "the the"
  "is is"
  "hello hello"

Doesn't match:
  "the cat"
  "hello world"
```

---

## Lookahead and Lookbehind

Match based on what comes before/after, without including it in the match.

### Lookahead

| Syntax | Name | Meaning |
|--------|------|---------|
| `(?=...)` | Positive lookahead | Followed by... |
| `(?!...)` | Negative lookahead | NOT followed by... |

```
Pattern: \d+(?=px)

Test: "width: 100px, height: 50em"

Matches:
  100  (followed by px)

Doesn't match:
  50   (followed by em, not px)
```

```
Pattern: \d+(?!px)

Test: "width: 100px, height: 50em"

Matches:
  10   (the "10" not followed by px)
  50   (not followed by px)
```

### Lookbehind

| Syntax | Name | Meaning |
|--------|------|---------|
| `(?<=...)` | Positive lookbehind | Preceded by... |
| `(?<!...)` | Negative lookbehind | NOT preceded by... |

```
Pattern: (?<=\$)\d+

Test: "Price: $100, Quantity: 50"

Matches:
  100  (preceded by $)

Doesn't match:
  50   (not preceded by $)
```

### Practical Example: Password Validation

```
Pattern: ^(?=.*[a-z])(?=.*[A-Z])(?=.*\d).{8,}$

Meaning:
  (?=.*[a-z])  - must contain lowercase
  (?=.*[A-Z])  - must contain uppercase
  (?=.*\d)     - must contain digit
  .{8,}        - at least 8 characters

Matches:
  Password1
  SecurePass99
  MyP4ssword

Doesn't match:
  password    (no uppercase)
  PASSWORD1   (no lowercase)
  Passwo1     (less than 8)
```

---

## Flags

Modifiers that change how the pattern behaves.

| Flag | Name | Effect |
|------|------|--------|
| `i` | Case insensitive | `A` matches `a` |
| `g` | Global | Find all matches, not just first |
| `m` | Multiline | `^` and `$` match line boundaries |
| `s` | Dotall | `.` matches newlines too |
| `u` | Unicode | Full Unicode support |
| `x` | Verbose | Ignore whitespace (for readability) |

### Usage by Language

```javascript
// JavaScript
/pattern/gi

new RegExp('pattern', 'gi')
```

```python
# Python
import re
re.search(r'pattern', text, re.IGNORECASE | re.MULTILINE)
```

```bash
# grep
grep -i 'pattern' file.txt    # case insensitive
grep -E 'pattern' file.txt    # extended regex
```

### Multiline Example

```
Text:
  Line 1
  Line 2
  Line 3

Pattern: ^Line

Without m flag: Matches only "Line" at position 0
With m flag:    Matches "Line" at start of each line (3 matches)
```

---

## Common Gotchas

### 1. Forgetting to Escape

```
Wrong: version 1.0
Right: version 1\.0

"version 1.0" matches "version 1.0"
"version 1.0" also matches "version 1X0" (without escape)
```

### 2. Greedy by Default

```
Wrong: <.*>       matches "<div>content</div>" as one match
Right: <.*?>      matches "<div>" and "</div>" separately
```

### 3. The Difference Between `\d` and `[0-9]`

In most engines they're the same, but `\d` can match Unicode digits in some languages:

```python
# Python 3
import re
re.findall(r'\d', '123٤٥٦')  # Might match Arabic numerals too!
```

Use `[0-9]` if you only want ASCII digits.

### 4. Anchors in Multiline Mode

```
Without m flag:
  ^ = start of entire string
  $ = end of entire string

With m flag:
  ^ = start of any line
  $ = end of any line
```

### 5. Character Class Gotchas

```
Inside []:
  - Most metacharacters lose their special meaning
  - ^ at start means negation
  - - between chars means range
  - ] needs escaping or put first

[.+*]     matches literal . + or *
[^abc]    matches anything except a, b, c
[a-z]     matches a through z
[]a]      matches ] or a (] at start)
[a\-z]    matches a, -, or z (escaped -)
```

### 6. Empty Matches

`*` can match zero characters:

```
Pattern: a*

String: "bbb"
Matches: "" (empty) at positions 0, 1, 2, 3

Use a+ if you need at least one.
```

---

## Quick Reference Card

```
BASICS
.       any character
\       escape next character
|       alternation (or)

CHARACTER CLASSES
\d      digit           [0-9]
\D      non-digit       [^0-9]
\w      word char       [a-zA-Z0-9_]
\W      non-word        [^a-zA-Z0-9_]
\s      whitespace
\S      non-whitespace

QUANTIFIERS
*       0 or more       {0,}
+       1 or more       {1,}
?       0 or 1          {0,1}
{n}     exactly n
{n,}    n or more
{n,m}   n to m

ANCHORS
^       start of string/line
$       end of string/line
\b      word boundary

GROUPS
(...)       capturing group
(?:...)     non-capturing group
\1          backreference

LOOKAROUND
(?=...)     positive lookahead
(?!...)     negative lookahead
(?<=...)    positive lookbehind
(?<!...)    negative lookbehind

FLAGS
i           case insensitive
g           global (all matches)
m           multiline
s           dotall (. matches \n)
```

---

*Now go copy patterns from [Recipes](recipes.md).*
