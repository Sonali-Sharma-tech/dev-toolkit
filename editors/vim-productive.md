# Vim Productivity

> Beyond survival. Commands that actually save time.

You can exit Vim now. Time to make it work for you.

## Table of Contents
- [Navigation That Matters](#navigation-that-matters)
- [Editing at Speed](#editing-at-speed)
- [Text Objects (Game Changer)](#text-objects-game-changer)
- [Visual Mode Mastery](#visual-mode-mastery)
- [Search and Replace](#search-and-replace)
- [Multi-File Editing](#multi-file-editing)
- [Macros (Repeat Anything)](#macros-repeat-anything)
- [Marks and Jumps](#marks-and-jumps)
- [Registers (Multiple Clipboards)](#registers-multiple-clipboards)
- [Command-Line Power](#command-line-power)
- [Practical Workflows](#practical-workflows)

---

## Navigation That Matters

### Jump to What You're Looking For

| Command | Action |
|---------|--------|
| `*` | Search word under cursor (forward) |
| `#` | Search word under cursor (backward) |
| `n` | Next match |
| `N` | Previous match |
| `gd` | Go to local definition |
| `gf` | Go to file under cursor |

### Move Within a Line

| Command | Where |
|---------|-------|
| `f{char}` | Find next {char} on line |
| `t{char}` | Till next {char} (cursor before it) |
| `;` | Repeat last f/t |
| `,` | Repeat last f/t (opposite direction) |
| `0` | First column |
| `^` | First non-blank character |
| `$` | End of line |

### Scenario: Jump to that closing brace

```
f}      " Jump to next }
```

### Scenario: Go to specific line

```vim
:42         " Go to line 42
42G         " Same thing
42gg        " Same thing
```

### Move by Semantic Units

| Command | Movement |
|---------|----------|
| `w` / `b` | Word forward/back |
| `e` | End of word |
| `{` / `}` | Paragraph forward/back |
| `%` | Matching bracket |
| `[[` / `]]` | Section/function forward/back |

### Screen Positioning

| Command | Cursor Position |
|---------|-----------------|
| `zz` | Center current line on screen |
| `zt` | Current line to top |
| `zb` | Current line to bottom |
| `H` | Jump to top of screen |
| `M` | Jump to middle of screen |
| `L` | Jump to bottom of screen |

---

## Editing at Speed

### The Dot Command (Most Important Key)

`.` repeats your last change. Use it obsessively.

#### Scenario: Change multiple variable names

```
ciw newName Esc    " Change first occurrence
n                  " Find next
.                  " Repeat the change
n.n.n.             " Keep going
```

### Combine Operators and Motions

Every command is: **operator + motion**

| Operator | Meaning |
|----------|---------|
| `d` | Delete |
| `c` | Change (delete and insert) |
| `y` | Yank (copy) |
| `>` | Indent |
| `<` | Unindent |
| `=` | Auto-indent |
| `gU` | Uppercase |
| `gu` | Lowercase |

| Motion | Target |
|--------|--------|
| `w` | Word |
| `W` | WORD (whitespace-delimited) |
| `$` | To end of line |
| `0` | To start of line |
| `}` | To next paragraph |
| `G` | To end of file |
| `gg` | To start of file |

#### Combinations

```
d$      Delete to end of line
c}      Change to end of paragraph
y4j     Copy 4 lines down
gUw     Uppercase word
=G      Auto-indent to end of file
```

### Quick Edits

| Command | Action |
|---------|--------|
| `r{char}` | Replace single character |
| `~` | Toggle case |
| `J` | Join line below |
| `xp` | Swap two characters |
| `ddp` | Swap two lines |

---

## Text Objects (Game Changer)

Text objects let you operate on semantic chunks of code.

### The Pattern: `{operator}{a|i}{object}`

- `a` = "a" (includes surrounding whitespace/delimiters)
- `i` = "inner" (just the content)

### Common Text Objects

| Object | Description |
|--------|-------------|
| `w` | word |
| `W` | WORD |
| `s` | sentence |
| `p` | paragraph |
| `"` | double quotes |
| `'` | single quotes |
| `` ` `` | backticks |
| `(` or `)` | parentheses |
| `[` or `]` | brackets |
| `{` or `}` | braces |
| `<` or `>` | angle brackets |
| `t` | HTML/XML tag |

### Scenario: Delete function arguments

```javascript
// Cursor anywhere in the parentheses:
myFunction(arg1, arg2, arg3)

di(     " Deletes: arg1, arg2, arg3
        " Result:  myFunction()
```

### Scenario: Change string content

```javascript
// Cursor anywhere in the string:
const msg = "Hello, World";

ci"     " Delete string, enter insert mode
        " Type new content
```

### Scenario: Select entire paragraph

```
vip     " Visual select inner paragraph
```

### Scenario: Delete HTML tag content

```html
<!-- Cursor inside the div -->
<div>Content to delete</div>

cit     " Change inner tag
        " Type new content
```

### More Examples

```
daw     " Delete a word (including trailing space)
ciw     " Change inner word
yi"     " Yank inside quotes
da"     " Delete including quotes
vi{     " Visual select inside braces
va(     " Visual select including parentheses
>ip     " Indent inner paragraph
```

---

## Visual Mode Mastery

### Three Types of Visual Mode

| Key | Mode | Selects |
|-----|------|---------|
| `v` | Character | Characters |
| `V` | Line | Whole lines |
| `Ctrl+v` | Block | Rectangular block |

### Visual Block Mode is Magic

#### Scenario: Add prefix to multiple lines

```
# Before:
line1
line2
line3

Ctrl+v     " Enter block mode
jj         " Select 3 lines
I          " Insert at beginning
// Esc     " Type prefix, then Esc

# After:
// line1
// line2
// line3
```

#### Scenario: Remove first N characters from lines

```
Ctrl+v     " Block mode
jjj        " Select lines
ll         " Extend selection 2 chars
d          " Delete
```

#### Scenario: Add suffix to multiple lines

```
Ctrl+v     " Block mode
jj         " Select lines
$          " Extend to end
A          " Append
,Esc       " Type suffix, then Esc
```

### Reselect Last Selection

```
gv         " Reselect what you last selected
```

### Visual Mode Operations

After selecting:

| Key | Action |
|-----|--------|
| `d` | Delete |
| `y` | Yank |
| `c` | Change |
| `>` | Indent |
| `<` | Unindent |
| `~` | Toggle case |
| `u` | Lowercase |
| `U` | Uppercase |
| `:` | Run command on selection |

---

## Search and Replace

### Search

| Command | Action |
|---------|--------|
| `/pattern` | Search forward |
| `?pattern` | Search backward |
| `n` | Next match |
| `N` | Previous match |
| `*` | Search word under cursor |
| `#` | Search word backward |

### Search Options

```vim
/pattern\c      " Case insensitive
/pattern\C      " Case sensitive
/\vpattern      " "Very magic" (regex mode)
```

### Clear Search Highlight

```vim
:noh            " Clear highlight
```

Or map it:
```vim
nnoremap <Esc> :noh<CR>
```

### Replace (Substitute)

```vim
:s/old/new/         " First on current line
:s/old/new/g        " All on current line
:%s/old/new/g       " All in file
:%s/old/new/gc      " All in file, confirm each
```

### Replace Flags

| Flag | Meaning |
|------|---------|
| `g` | All occurrences on line |
| `c` | Confirm each |
| `i` | Case insensitive |
| `I` | Case sensitive |
| `n` | Count matches only |

### Practical Replace Examples

```vim
" Remove trailing whitespace
:%s/\s\+$//g

" Delete empty lines
:g/^$/d

" Add semicolon to end of each line
:%s/$/;/

" Change all function declarations
:%s/function \(\w\+\)/const \1 =/g

" Replace in visual selection
:'<,'>s/old/new/g
```

### Using Captured Groups

```vim
" Swap first and last name
:%s/\(\w\+\) \(\w\+\)/\2 \1/g

" Wrap words in quotes
:%s/\(\w\+\)/"\1"/g
```

---

## Multi-File Editing

### Buffers (Open Files)

| Command | Action |
|---------|--------|
| `:e filename` | Open file |
| `:ls` | List buffers |
| `:b{n}` | Go to buffer n |
| `:bn` | Next buffer |
| `:bp` | Previous buffer |
| `:bd` | Close buffer |
| `Ctrl+^` | Switch to last buffer |

### Split Windows

| Command | Action |
|---------|--------|
| `:sp` | Horizontal split |
| `:vsp` | Vertical split |
| `:sp file` | Split and open file |
| `Ctrl+w h/j/k/l` | Navigate splits |
| `Ctrl+w c` | Close split |
| `Ctrl+w o` | Close all but current |
| `Ctrl+w =` | Equal size splits |

### Tabs

| Command | Action |
|---------|--------|
| `:tabnew` | New tab |
| `:tabnew file` | New tab with file |
| `gt` | Next tab |
| `gT` | Previous tab |
| `:tabc` | Close tab |

### Scenario: Edit multiple files

```bash
vim file1.js file2.js file3.js
```

```vim
:bn             " Next file
:bp             " Previous file
:ls             " See all open files
```

### Scenario: Search and replace across files

```vim
" Using args
:args **/*.js               " Load all JS files
:argdo %s/old/new/g | w     " Replace and save in each
```

---

## Macros (Repeat Anything)

Record a sequence of commands, replay it anywhere.

### Record and Play

| Command | Action |
|---------|--------|
| `q{a-z}` | Start recording to register |
| `q` | Stop recording |
| `@{a-z}` | Play macro |
| `@@` | Replay last macro |
| `5@a` | Play macro 5 times |

### Scenario: Format multiple lines the same way

```
# Turn this:
firstName
lastName
email

# Into this:
private firstName: string;
private lastName: string;
private email: string;

# Record:
qa                    " Start recording to 'a'
Iprivate Esc          " Insert "private " at start
A: string;Esc         " Append ": string;"
j                     " Move to next line
q                     " Stop recording

# Replay:
2@a                   " Apply to next 2 lines
```

### Scenario: Create numbered list

```
# From:
item
item
item

# To:
1. item
2. item
3. item

# Use line number:
qa
I1. Esc
j
q

# But incrementing needs a trick:
:let i=1 | g/^/s/^/\=i.'. '/ | let i+=1
```

### Edit a Macro

```vim
"ap             " Paste macro from register a
# Edit the text
"ayy            " Yank back to register a
```

---

## Marks and Jumps

### Set and Jump to Marks

| Command | Action |
|---------|--------|
| `m{a-z}` | Set local mark |
| `m{A-Z}` | Set global mark (across files) |
| `` `{mark}`` | Jump to mark (exact position) |
| `'{mark}` | Jump to mark (line start) |
| `:marks` | List marks |

### Scenario: Jump between two spots in code

```
ma              " Mark current position as 'a'
# Navigate elsewhere
mb              " Mark this spot as 'b'

`a              " Jump back to 'a'
`b              " Jump to 'b'
```

### Special Marks (Auto-set)

| Mark | Position |
|------|----------|
| ``` `` ``` | Last jump position |
| `` `. `` | Last edit position |
| `` `^ `` | Last insert position |

### Jump List

| Command | Action |
|---------|--------|
| `Ctrl+o` | Jump back |
| `Ctrl+i` | Jump forward |
| `:jumps` | List jump history |

---

## Registers (Multiple Clipboards)

### Named Registers

| Register | Purpose |
|----------|---------|
| `"a`-`"z` | Named storage |
| `""` | Default (unnamed) |
| `"0` | Last yank |
| `"1`-`"9` | Delete history |
| `"+` | System clipboard |
| `"*` | X11 selection |
| `"_` | Black hole (delete to nowhere) |

### Using Registers

```vim
"ayy            " Yank line to register a
"ap             " Paste from register a
"+y             " Yank to system clipboard
"+p             " Paste from system clipboard
"_dd            " Delete line without storing it
```

### View Registers

```vim
:reg            " Show all registers
:reg a          " Show register a
```

### Scenario: Copy something without losing current paste

```vim
"ayy            " Yank to register a
dd              " Delete something (goes to default register)
p               " Paste the deleted text
"ap             " Paste the original yanked text
```

---

## Command-Line Power

### Shell Commands

```vim
:!ls            " Run ls
:r !date        " Insert command output
:w !pbcopy      " Send buffer to command (macOS copy)
:%!sort         " Sort entire file
:%!jq .         " Format JSON with jq
```

### Global Commands

`:g/pattern/command` - Run command on lines matching pattern

```vim
:g/TODO/d               " Delete all TODO lines
:g/^$/d                 " Delete empty lines
:g/console.log/d        " Remove all console.log
:g/error/p              " Print all lines with 'error'
:g/pattern/normal @a    " Run macro on matching lines
```

`:v/pattern/command` - Run on lines NOT matching

```vim
:v/keep/d               " Delete lines WITHOUT 'keep'
```

### Useful Commands

```vim
:sort           " Sort lines
:sort!          " Reverse sort
:sort u         " Sort and remove duplicates
:sort n         " Numeric sort

:%normal A;     " Append ; to every line
:%normal I//    " Comment every line

:earlier 5m     " Go back 5 minutes
:later 2m       " Go forward 2 minutes
```

---

## Practical Workflows

### Workflow: Rename a Variable

```
*               " Search for word under cursor
ciw newName Esc " Change first occurrence
n               " Next match
.               " Repeat the change
n.n.n.          " Continue through file
```

### Workflow: Wrap Lines in Quotes

```
V               " Visual line mode
:s/^\(.*\)$/"\1"/
```

### Workflow: Align Equals Signs

```vim
" Install vim-easy-align or use visual block
Ctrl+v          " Block select
jjj             " Select lines
:s/=/         =/  " Pad with spaces
```

### Workflow: Comment Multiple Lines

```
Ctrl+v          " Block mode
jjj             " Select lines
I// Esc         " Insert comment
```

### Workflow: Uncomment Multiple Lines

```
Ctrl+v          " Block mode
jjj             " Select lines
ll              " Select //
d               " Delete
```

### Workflow: Extract Function Parameters

```javascript
// Cursor inside: someFunc(a, b, c, d, e)
yi(             " Yank inside parentheses
```

### Workflow: Swap Two Arguments

```javascript
// From: func(arg1, arg2)
// To:   func(arg2, arg1)

f,              " Find comma
dwwP            " Delete word, move, paste before
```

---

## Quick Reference

```
NAVIGATION
─────────────────────────────
*/#          Search word under cursor
f{c}/t{c}    Find/till character
%            Matching bracket
gg/G         Start/end of file
Ctrl+o/i     Jump back/forward

EDITING
─────────────────────────────
.            Repeat last change
ciw          Change inner word
ci"          Change inside quotes
da(          Delete including parens
>ip          Indent paragraph

VISUAL
─────────────────────────────
v/V/Ctrl+v   Char/line/block visual
gv           Reselect last
o            Move to other end

MULTI-FILE
─────────────────────────────
:e file      Open file
:ls          List buffers
Ctrl+^       Last buffer
:sp/:vsp     Split windows

MACROS
─────────────────────────────
qa           Start recording to a
q            Stop recording
@a           Play macro a
@@           Replay last macro

SEARCH/REPLACE
─────────────────────────────
:%s/a/b/g    Replace all
:%s/a/b/gc   Replace with confirm
:g/pat/d     Delete matching lines
```

---

*Next: [Vim Configuration](vim-config.md) - Make it feel like home*
