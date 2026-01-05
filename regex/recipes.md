# Regex Recipes

> Copy the pattern. Test against the strings. Done.

Every pattern includes test strings so you can verify it works before using it.

## Table of Contents
- [Email & URLs](#email--urls)
- [Phone Numbers](#phone-numbers)
- [Dates & Times](#dates--times)
- [Numbers & Currency](#numbers--currency)
- [Validation](#validation)
- [Code Patterns](#code-patterns)
- [Text Processing](#text-processing)
- [File Paths](#file-paths)
- [Security Patterns](#security-patterns)
- [Find & Replace](#find--replace)

---

## Email & URLs

### Email Address (Simple)

```
Pattern: [\w.+-]+@[\w-]+\.[\w.-]+
```

```
Matches:
  user@example.com
  first.last@company.co.uk
  name+tag@gmail.com
  user123@sub.domain.org

Doesn't match:
  @example.com
  user@
  user@.com
  user @example.com
```

### Email Address (Strict)

```
Pattern: ^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$
```

```
Matches:
  john.doe@example.com
  user+label@gmail.com
  contact@company.co.uk

Doesn't match:
  john..doe@example.com
  user@localhost
  @example.com
```

### URL (HTTP/HTTPS)

```
Pattern: https?://[^\s]+
```

```
Matches:
  https://example.com
  http://example.com/path?query=1
  https://sub.domain.co.uk/page

Doesn't match:
  ftp://files.example.com
  example.com
  //example.com
```

### URL with Optional Protocol

```
Pattern: (https?://)?(www\.)?[\w-]+\.[\w.-]+(/[\w-./?%&=]*)?
```

```
Matches:
  https://www.example.com/page
  www.example.com
  example.com/path
  sub.domain.com/page?id=123

Doesn't match:
  localhost
  192.168.1.1
```

### Domain Name

```
Pattern: ^([a-z0-9]+(-[a-z0-9]+)*\.)+[a-z]{2,}$
```

```
Matches:
  example.com
  sub.example.com
  my-site.co.uk

Doesn't match:
  -example.com
  example-.com
  example
```

### URL Slug

```
Pattern: ^[a-z0-9]+(-[a-z0-9]+)*$
```

```
Matches:
  my-awesome-post
  hello-world
  product-123

Doesn't match:
  My-Post
  post--double
  -starts-with-dash
```

---

## Phone Numbers

### US Phone Number

```
Pattern: ^(\+1)?[-.\s]?\(?\d{3}\)?[-.\s]?\d{3}[-.\s]?\d{4}$
```

```
Matches:
  (555) 123-4567
  555-123-4567
  555.123.4567
  +1 555 123 4567
  5551234567

Doesn't match:
  555-1234
  (555 123-4567
  123-456-78901
```

### International Phone (E.164)

```
Pattern: ^\+[1-9]\d{1,14}$
```

```
Matches:
  +14155551234
  +442071234567
  +919876543210

Doesn't match:
  +0123456789
  14155551234
  +1-415-555-1234
```

### UK Phone Number

```
Pattern: ^(\+44|0)\d{10}$
```

```
Matches:
  +447123456789
  01onal234567890
  07123456789

Doesn't match:
  7123456789
  +44-712-345-6789
```

### Phone with Extensions

```
Pattern: ^[\d\s\-().]+(?:\s*(ext|x|ext.)\s*\d+)?$
```

```
Matches:
  555-123-4567
  555-123-4567 ext 123
  555-123-4567 x456

Doesn't match:
  abc-def-ghij
```

---

## Dates & Times

### Date (YYYY-MM-DD)

```
Pattern: ^\d{4}-(0[1-9]|1[0-2])-(0[1-9]|[12]\d|3[01])$
```

```
Matches:
  2024-01-15
  2023-12-31
  2000-06-01

Doesn't match:
  2024-13-01
  2024-00-15
  2024-1-5
  24-01-15
```

### Date (MM/DD/YYYY)

```
Pattern: ^(0[1-9]|1[0-2])/(0[1-9]|[12]\d|3[01])/\d{4}$
```

```
Matches:
  01/15/2024
  12/31/2023

Doesn't match:
  1/15/2024
  13/01/2024
```

### Date (DD/MM/YYYY)

```
Pattern: ^(0[1-9]|[12]\d|3[01])/(0[1-9]|1[0-2])/\d{4}$
```

```
Matches:
  15/01/2024
  31/12/2023

Doesn't match:
  32/01/2024
  15/13/2024
```

### Time (24-Hour)

```
Pattern: ^([01]\d|2[0-3]):([0-5]\d)$
```

```
Matches:
  00:00
  14:30
  23:59

Doesn't match:
  24:00
  14:60
  2:30
```

### Time (12-Hour with AM/PM)

```
Pattern: ^(0?[1-9]|1[0-2]):[0-5]\d\s?[AaPp][Mm]$
```

```
Matches:
  9:30 AM
  12:00 PM
  11:59pm

Doesn't match:
  13:00 PM
  0:30 AM
```

### ISO 8601 DateTime

```
Pattern: ^\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}(\.\d{3})?(Z|[+-]\d{2}:\d{2})?$
```

```
Matches:
  2024-01-15T14:30:00Z
  2024-01-15T14:30:00.000Z
  2024-01-15T14:30:00+05:30

Doesn't match:
  2024-01-15 14:30:00
  2024-01-15T14:30
```

---

## Numbers & Currency

### Integer

```
Pattern: ^-?\d+$
```

```
Matches:
  42
  -17
  0
  123456789

Doesn't match:
  3.14
  1,000
  1e10
```

### Decimal Number

```
Pattern: ^-?\d+(\.\d+)?$
```

```
Matches:
  3.14
  -2.5
  100
  0.001

Doesn't match:
  .5
  3.
  1,234.56
```

### Number with Commas

```
Pattern: ^-?\d{1,3}(,\d{3})*(\.\d+)?$
```

```
Matches:
  1,234
  1,234,567.89
  -999,999

Doesn't match:
  1234567
  1,23,456
```

### Percentage

```
Pattern: ^-?\d+(\.\d+)?%$
```

```
Matches:
  50%
  99.9%
  -5.5%
  100%

Doesn't match:
  50
  50 %
```

### Currency (USD)

```
Pattern: ^\$\d{1,3}(,\d{3})*(\.\d{2})?$
```

```
Matches:
  $10
  $1,000
  $1,234.56
  $1,234,567.89

Doesn't match:
  $1234
  $10.5
  10.00
```

### Currency (Multiple)

```
Pattern: ^[$€£¥]\d{1,3}(,\d{3})*(\.\d{2})?$
```

```
Matches:
  $100.00
  €1,234.56
  £999.99
  ¥10,000

Doesn't match:
  100 USD
  $100.5
```

### Credit Card Number

```
Pattern: ^\d{4}[\s-]?\d{4}[\s-]?\d{4}[\s-]?\d{4}$
```

```
Matches:
  4111111111111111
  4111-1111-1111-1111
  4111 1111 1111 1111

Doesn't match:
  411111111111111
  4111-1111-1111-111
```

---

## Validation

### Username

```
Pattern: ^[a-zA-Z][a-zA-Z0-9_]{2,15}$
```

```
Matches:
  john_doe
  User123
  abc

Doesn't match:
  1user
  _username
  ab
  a_very_long_username_here
```

### Strong Password

```
Pattern: ^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$
```

```
Matches:
  Password1!
  MyP@ssw0rd
  Str0ng!Pass

Doesn't match:
  password
  PASSWORD1!
  Password1
  Pass1!
```

### UUID

```
Pattern: ^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$
```

```
Matches:
  550e8400-e29b-41d4-a716-446655440000
  123e4567-e89b-12d3-a456-426614174000

Doesn't match:
  550e8400-e29b-41d4-a716
  550e8400e29b41d4a716446655440000
```

### ZIP Code (US)

```
Pattern: ^\d{5}(-\d{4})?$
```

```
Matches:
  12345
  12345-6789

Doesn't match:
  1234
  123456
  12345-678
```

### IPv4 Address

```
Pattern: ^((25[0-5]|2[0-4]\d|[01]?\d\d?)\.){3}(25[0-5]|2[0-4]\d|[01]?\d\d?)$
```

```
Matches:
  192.168.1.1
  10.0.0.1
  255.255.255.0

Doesn't match:
  256.1.1.1
  192.168.1
  192.168.1.1.1
```

### MAC Address

```
Pattern: ^([0-9A-Fa-f]{2}[:-]){5}[0-9A-Fa-f]{2}$
```

```
Matches:
  00:1A:2B:3C:4D:5E
  00-1a-2b-3c-4d-5e

Doesn't match:
  001A2B3C4D5E
  00:1A:2B:3C:4D
```

### Hex Color

```
Pattern: ^#([0-9A-Fa-f]{3}|[0-9A-Fa-f]{6})$
```

```
Matches:
  #fff
  #FFF
  #ffffff
  #123ABC

Doesn't match:
  #ffff
  fff
  #GGGGGG
```

### Semantic Version

```
Pattern: ^v?\d+\.\d+\.\d+(-[\w.]+)?(\+[\w.]+)?$
```

```
Matches:
  1.0.0
  v2.1.3
  1.0.0-beta.1
  1.0.0+build.123

Doesn't match:
  1.0
  v1
  1.0.0.0
```

---

## Code Patterns

### Variable Name

```
Pattern: ^[a-zA-Z_][a-zA-Z0-9_]*$
```

```
Matches:
  myVariable
  _private
  camelCase
  CONSTANT_NAME

Doesn't match:
  123var
  my-variable
  my.variable
```

### Function Call

```
Pattern: (\w+)\s*\([^)]*\)
```

```
Matches:
  print("hello")
  myFunc(arg1, arg2)
  console.log()

Capture group 1:
  print
  myFunc
  log
```

### Import Statement (JavaScript)

```
Pattern: import\s+(?:{([^}]+)}|(\w+))\s+from\s+['"]([^'"]+)['"]
```

```
Matches:
  import { useState } from 'react'
  import React from 'react'
  import { a, b, c } from "./utils"

Capture groups:
  1: Named imports
  2: Default import
  3: Module path
```

### HTML Tag

```
Pattern: <(\w+)[^>]*>
```

```
Matches:
  <div>
  <input type="text">
  <br/>

Capture group 1:
  div
  input
  br
```

### HTML Attribute

```
Pattern: (\w+)=["']([^"']+)["']
```

```
Matches:
  class="container"
  id='main'
  data-value="123"

Captures:
  1: class, id, data-value
  2: container, main, 123
```

### CSS Class

```
Pattern: \.([a-zA-Z_][\w-]*)
```

```
Matches:
  .container
  .btn-primary
  ._hidden

Capture group 1:
  container
  btn-primary
  _hidden
```

### TODO Comments

```
Pattern: (?:\/\/|#|\/\*)\s*(TODO|FIXME|HACK|XXX):\s*(.+)
```

```
Matches:
  // TODO: Refactor this
  # FIXME: Handle edge case
  /* HACK: Temporary workaround */

Captures:
  1: TODO, FIXME, HACK
  2: The comment text
```

### Git Commit Hash

```
Pattern: \b[0-9a-f]{7,40}\b
```

```
Matches:
  a1b2c3d
  abc123def456789012345678901234567890abcd

Doesn't match:
  abc12
  xyz1234
```

---

## Text Processing

### Extract Quoted String

```
Pattern: ["']([^"']+)["']
```

```
Matches:
  "hello world"
  'single quotes'

Capture group 1:
  hello world
  single quotes
```

### Extract Between Parentheses

```
Pattern: \(([^)]+)\)
```

```
Matches:
  (content here)
  (123)

Capture group 1:
  content here
  123
```

### Extract Between Brackets

```
Pattern: \[([^\]]+)\]
```

```
Matches:
  [array item]
  [0]

Capture group 1:
  array item
  0
```

### Remove HTML Tags

```
Pattern: <[^>]+>
```

```
Replace with: (empty string)

Before: <p>Hello <b>world</b></p>
After:  Hello world
```

### Remove Extra Whitespace

```
Pattern: \s+
```

```
Replace with: (single space)

Before: hello    world
After:  hello world
```

### Trim Whitespace

```
Pattern: ^\s+|\s+$
```

```
Replace with: (empty string)

Before:   hello world
After: hello world
```

### Find Repeated Words

```
Pattern: \b(\w+)\s+\1\b
```

```
Matches:
  "the the cat"
  "is is wrong"

Capture group 1:
  the
  is
```

### CamelCase to snake_case

```
Pattern: ([a-z])([A-Z])
```

```
Replace with: $1_$2 (then lowercase)

Before: camelCaseString
After:  camel_case_string
```

### snake_case to camelCase

```
Pattern: _([a-z])
```

```
Replace with: \U$1 (uppercase $1)

Before: snake_case_string
After:  snakeCaseString
```

---

## File Paths

### Unix Path

```
Pattern: ^(/[^/\0]+)+/?$
```

```
Matches:
  /home/user
  /var/log/app.log
  /etc/

Doesn't match:
  home/user
  /home//user
```

### Windows Path

```
Pattern: ^[A-Za-z]:\\(?:[^\\/:*?"<>|\r\n]+\\)*[^\\/:*?"<>|\r\n]*$
```

```
Matches:
  C:\Users\John
  D:\Projects\app.exe

Doesn't match:
  C:Users
  /home/user
```

### File with Line Number

```
Pattern: (.+):(\d+)(?::(\d+))?
```

```
Matches:
  src/app.js:42
  file.py:10:5

Captures:
  1: src/app.js, file.py
  2: 42, 10
  3: (empty), 5
```

### Image Files

```
Pattern: \.(jpe?g|png|gif|bmp|svg|webp)$
```

```
Matches:
  photo.jpg
  icon.png
  animation.gif

Doesn't match:
  document.pdf
  image.txt
```

### JavaScript/TypeScript Files

```
Pattern: \.(jsx?|tsx?)$
```

```
Matches:
  app.js
  component.jsx
  types.ts
  App.tsx

Doesn't match:
  styles.css
  data.json
```

---

## Security Patterns

### AWS Access Key

```
Pattern: AKIA[0-9A-Z]{16}
```

```
Matches:
  AKIAIOSFODNN7EXAMPLE

Doesn't match:
  ASIA...  (session token)
  AKIA123  (too short)
```

### AWS Secret Key

```
Pattern: [A-Za-z0-9/+=]{40}
```

```
Matches:
  wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

Note: Always verify with context
```

### Private Key Header

```
Pattern: -----BEGIN\s+(?:RSA\s+)?PRIVATE\s+KEY-----
```

```
Matches:
  -----BEGIN PRIVATE KEY-----
  -----BEGIN RSA PRIVATE KEY-----
```

### JWT Token

```
Pattern: eyJ[A-Za-z0-9-_]+\.eyJ[A-Za-z0-9-_]+\.[A-Za-z0-9-_.+/=]+
```

```
Matches:
  eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIn0.dozjgNryP4J3jVmNHl0w5N_XgL0n3I9PlFUP0THsR8U
```

### SQL Injection Attempt

```
Pattern: ('|--|;|/\*|\*/)
```

```
Matches (potential injection):
  ' OR 1=1 --
  ; DROP TABLE users
  /* comment */
```

### Path Traversal Attempt

```
Pattern: \.\.[/\\]
```

```
Matches:
  ../../../etc/passwd
  ..\..\windows\system32

Use to detect/block malicious requests
```

---

## Find & Replace

### Add Quotes Around Words

```
Find:    \b(\w+)\b
Replace: "$1"

Before: hello world
After:  "hello" "world"
```

### Swap Two Values

```
Find:    (\w+)=(\w+)
Replace: $2=$1

Before: key=value
After:  value=key
```

### Add Prefix to Lines

```
Find:    ^(.+)$
Replace: PREFIX: $1

Before: line content
After:  PREFIX: line content
```

### Convert URLs to Links

```
Find:    (https?://\S+)
Replace: <a href="$1">$1</a>

Before: Visit https://example.com
After:  Visit <a href="https://example.com">https://example.com</a>
```

### Remove Console Logs

```
Find:    console\.log\([^)]*\);?\n?
Replace: (empty)

Before: console.log("debug");
After:  (removed)
```

### Convert Single Quotes to Double

```
Find:    '([^']*)'
Replace: "$1"

Before: const x = 'hello';
After:  const x = "hello";
```

### Add Semicolons

```
Find:    ([^;{}\s])$
Replace: $1;

Before: const x = 1
After:  const x = 1;
```

---

## Quick Copy Reference

| Pattern | Use Case |
|---------|----------|
| `[\w.+-]+@[\w-]+\.[\w.-]+` | Email |
| `https?://[^\s]+` | URL |
| `^\d{4}-\d{2}-\d{2}$` | Date (ISO) |
| `^\d+(\.\d+)?$` | Decimal number |
| `^[a-zA-Z][a-zA-Z0-9_]{2,15}$` | Username |
| `^#([0-9A-Fa-f]{3}\|[0-9A-Fa-f]{6})$` | Hex color |
| `\b(\w+)\s+\1\b` | Repeated words |
| `<[^>]+>` | HTML tags |
| `["']([^"']+)["']` | Quoted strings |

---

*Test all patterns at [regex101.com](https://regex101.com) before using in production.*
