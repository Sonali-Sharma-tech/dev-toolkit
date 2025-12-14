# Vim/Neovim Commands Reference

Essential Vim commands and shortcuts. From survival basics to power-user techniques.

## Table of Contents
- [Modes](#modes)
- [Exiting Vim](#exiting-vim)
- [Navigation](#navigation)
- [Editing](#editing)
- [Visual Mode](#visual-mode)
- [Copy, Cut, Paste](#copy-cut-paste)
- [Search and Replace](#search-and-replace)
- [Undo and Redo](#undo-and-redo)
- [Working with Files](#working-with-files)
- [Windows and Tabs](#windows-and-tabs)
- [Marks and Jumps](#marks-and-jumps)
- [Macros](#macros)
- [Registers](#registers)
- [Text Objects](#text-objects)
- [Folding](#folding)
- [Command Mode](#command-mode)
- [Configuration](#configuration)
- [Neovim Specific](#neovim-specific)
- [Plugins](#plugins)

---

## Modes

Vim has multiple modes. Understanding modes is essential.

| Mode | Key to Enter | Purpose |
|------|--------------|---------|
| Normal | `Esc` | Default mode, navigation and commands |
| Insert | `i`, `a`, `o`, etc. | Typing text |
| Visual | `v`, `V`, `Ctrl+v` | Selecting text |
| Command | `:` | Running commands |
| Replace | `R` | Overwrite text |

### Enter Insert Mode
| Key | Action |
|-----|--------|
| `i` | Insert before cursor |
| `I` | Insert at beginning of line |
| `a` | Append after cursor |
| `A` | Append at end of line |
| `o` | Open new line below |
| `O` | Open new line above |
| `s` | Substitute character (delete and insert) |
| `S` | Substitute line |
| `c{motion}` | Change (delete and insert) |

### Return to Normal Mode
| Key | Action |
|-----|--------|
| `Esc` | Return to normal mode |
| `Ctrl+[` | Same as Esc |
| `Ctrl+c` | Same as Esc (without abbreviation expansion) |

---

## Exiting Vim

The most important commands for beginners!

| Command | Action |
|---------|--------|
| `:q` | Quit (fails if unsaved changes) |
| `:q!` | Quit without saving (force) |
| `:w` | Save (write) |
| `:w filename` | Save as filename |
| `:wq` | Save and quit |
| `:x` | Save and quit (only writes if changed) |
| `ZZ` | Save and quit (normal mode) |
| `ZQ` | Quit without saving (normal mode) |
| `:wqa` | Save all and quit |
| `:qa!` | Quit all without saving |

---

## Navigation

### Basic Movement
| Key | Action |
|-----|--------|
| `h` | Left |
| `j` | Down |
| `k` | Up |
| `l` | Right |
| `gj` | Down (visual line, for wrapped text) |
| `gk` | Up (visual line) |

### Word Movement
| Key | Action |
|-----|--------|
| `w` | Next word (start) |
| `W` | Next WORD (whitespace-separated) |
| `e` | Next word (end) |
| `E` | Next WORD (end) |
| `b` | Previous word (start) |
| `B` | Previous WORD |
| `ge` | Previous word (end) |

### Line Movement
| Key | Action |
|-----|--------|
| `0` | Beginning of line |
| `^` | First non-blank character |
| `$` | End of line |
| `g_` | Last non-blank character |
| `+` | First character of next line |
| `-` | First character of previous line |

### Screen Movement
| Key | Action |
|-----|--------|
| `H` | Top of screen (High) |
| `M` | Middle of screen |
| `L` | Bottom of screen (Low) |
| `gg` | First line of file |
| `G` | Last line of file |
| `{number}G` | Go to line number |
| `:{number}` | Go to line number |
| `Ctrl+f` | Page forward (full screen) |
| `Ctrl+b` | Page backward |
| `Ctrl+d` | Half page down |
| `Ctrl+u` | Half page up |
| `Ctrl+e` | Scroll down one line |
| `Ctrl+y` | Scroll up one line |
| `zz` | Center cursor on screen |
| `zt` | Cursor to top of screen |
| `zb` | Cursor to bottom of screen |

### Character Search (in line)
| Key | Action |
|-----|--------|
| `f{char}` | Find next {char} |
| `F{char}` | Find previous {char} |
| `t{char}` | Till next {char} (cursor before) |
| `T{char}` | Till previous {char} |
| `;` | Repeat last f/F/t/T |
| `,` | Repeat last f/F/t/T (opposite direction) |

### Paragraph/Block Movement
| Key | Action |
|-----|--------|
| `{` | Previous paragraph/blank line |
| `}` | Next paragraph/blank line |
| `%` | Matching bracket/parenthesis |
| `[[` | Previous section/function |
| `]]` | Next section/function |

---

## Editing

### Delete
| Key | Action |
|-----|--------|
| `x` | Delete character under cursor |
| `X` | Delete character before cursor |
| `dd` | Delete line |
| `D` | Delete to end of line |
| `d{motion}` | Delete motion (dw, d$, d3j, etc.) |
| `daw` | Delete a word (including space) |
| `diw` | Delete inner word |
| `d/pattern` | Delete until pattern |

### Change (delete and enter insert mode)
| Key | Action |
|-----|--------|
| `cc` | Change entire line |
| `C` | Change to end of line |
| `c{motion}` | Change motion |
| `cw` | Change word |
| `ciw` | Change inner word |
| `ci"` | Change inside quotes |
| `ci(` | Change inside parentheses |
| `ct{char}` | Change till character |

### Replace
| Key | Action |
|-----|--------|
| `r{char}` | Replace single character |
| `R` | Enter replace mode (overwrite) |
| `~` | Toggle case of character |
| `g~{motion}` | Toggle case of motion |
| `gu{motion}` | Lowercase |
| `gU{motion}` | Uppercase |

### Join Lines
| Key | Action |
|-----|--------|
| `J` | Join line below with space |
| `gJ` | Join line below without space |

### Indent
| Key | Action |
|-----|--------|
| `>>` | Indent line |
| `<<` | Unindent line |
| `={motion}` | Auto-indent |
| `==` | Auto-indent current line |
| `gg=G` | Auto-indent entire file |

### Repeat and Numbers
| Key | Action |
|-----|--------|
| `.` | Repeat last change |
| `{number}{command}` | Repeat command N times |
| `5dd` | Delete 5 lines |
| `3w` | Move 3 words forward |
| `10j` | Move 10 lines down |

---

## Visual Mode

### Enter Visual Mode
| Key | Action |
|-----|--------|
| `v` | Character-wise visual |
| `V` | Line-wise visual |
| `Ctrl+v` | Block visual (column selection) |
| `gv` | Reselect last visual selection |

### Visual Mode Operations
| Key | Action |
|-----|--------|
| `d` or `x` | Delete selection |
| `c` | Change selection |
| `y` | Yank (copy) selection |
| `>` | Indent selection |
| `<` | Unindent selection |
| `~` | Toggle case |
| `u` | Lowercase |
| `U` | Uppercase |
| `o` | Move to other end of selection |
| `O` | Move to other corner (block mode) |

### Block Visual Mode Tips
```
# Select column and:
I{text}Esc     # Insert at beginning of each line
A{text}Esc     # Append at end of each line
c{text}Esc     # Replace selection on each line
r{char}        # Replace all selected chars
```

---

## Copy, Cut, Paste

### Yank (Copy)
| Key | Action |
|-----|--------|
| `yy` or `Y` | Yank line |
| `y{motion}` | Yank motion |
| `yw` | Yank word |
| `yiw` | Yank inner word |
| `yi"` | Yank inside quotes |
| `y$` | Yank to end of line |
| `y^` | Yank to beginning of line |

### Put (Paste)
| Key | Action |
|-----|--------|
| `p` | Paste after cursor/below line |
| `P` | Paste before cursor/above line |
| `gp` | Paste and move cursor after |
| `gP` | Paste before and move cursor after |
| `]p` | Paste with auto-indent |

### Delete (also yanks to register)
| Key | Action |
|-----|--------|
| `dd` | Delete line (cut) |
| `d{motion}` | Delete motion (cut) |
| `x` | Delete character (cut) |

---

## Search and Replace

### Search
| Key | Action |
|-----|--------|
| `/pattern` | Search forward |
| `?pattern` | Search backward |
| `n` | Next match |
| `N` | Previous match |
| `*` | Search word under cursor (forward) |
| `#` | Search word under cursor (backward) |
| `g*` | Like * but partial match |
| `g#` | Like # but partial match |

### Search Options
```vim
/pattern\c        " Case insensitive
/pattern\C        " Case sensitive
/\vpattern        " Very magic (regex)
/pattern/e        " Cursor at end of match
```

### Replace (Substitute)
```vim
:s/old/new/           " Replace first on current line
:s/old/new/g          " Replace all on current line
:%s/old/new/g         " Replace all in file
:%s/old/new/gc        " Replace all with confirmation
:5,10s/old/new/g      " Replace in lines 5-10
:'<,'>s/old/new/g     " Replace in visual selection
```

### Replace Flags
| Flag | Action |
|------|--------|
| `g` | Global (all occurrences on line) |
| `c` | Confirm each replacement |
| `i` | Case insensitive |
| `I` | Case sensitive |
| `n` | Count matches (don't replace) |

### Replace Special Characters
```vim
:%s/\n/\r/g           " Replace newlines
:%s/\t/  /g           " Replace tabs with spaces
:%s/\s\+$//g          " Remove trailing whitespace
:%s/^/# /g            " Add prefix to all lines
```

### Clear Search Highlight
```vim
:noh                  " Clear highlight
:nohlsearch           " Clear highlight (full command)
```

---

## Undo and Redo

| Key | Action |
|-----|--------|
| `u` | Undo |
| `U` | Undo all changes on line |
| `Ctrl+r` | Redo |
| `:earlier 5m` | Go back 5 minutes |
| `:later 10s` | Go forward 10 seconds |
| `:earlier 3f` | Go back 3 file saves |

---

## Working with Files

### Open and Edit
| Command | Action |
|---------|--------|
| `:e filename` | Edit file |
| `:e!` | Reload current file (discard changes) |
| `:e .` | Open file explorer |
| `:r filename` | Insert file contents |
| `:r !command` | Insert command output |
| `gf` | Go to file under cursor |
| `Ctrl+^` | Switch to alternate file |

### Save
| Command | Action |
|---------|--------|
| `:w` | Save |
| `:w filename` | Save as |
| `:w !sudo tee %` | Save with sudo |
| `:saveas filename` | Save as and switch |
| `:wa` | Save all buffers |

### Buffers
| Command | Action |
|---------|--------|
| `:ls` | List buffers |
| `:b{n}` | Go to buffer n |
| `:bn` | Next buffer |
| `:bp` | Previous buffer |
| `:bd` | Delete (close) buffer |
| `:bd!` | Force close buffer |
| `:bufdo {cmd}` | Run command on all buffers |

---

## Windows and Tabs

### Split Windows
| Command | Action |
|---------|--------|
| `:sp` or `Ctrl+w s` | Horizontal split |
| `:vsp` or `Ctrl+w v` | Vertical split |
| `:sp filename` | Split and open file |
| `:vsp filename` | Vertical split and open file |

### Navigate Windows
| Key | Action |
|-----|--------|
| `Ctrl+w h` | Move to left window |
| `Ctrl+w j` | Move to window below |
| `Ctrl+w k` | Move to window above |
| `Ctrl+w l` | Move to right window |
| `Ctrl+w w` | Cycle through windows |
| `Ctrl+w p` | Previous window |

### Resize Windows
| Key | Action |
|-----|--------|
| `Ctrl+w +` | Increase height |
| `Ctrl+w -` | Decrease height |
| `Ctrl+w >` | Increase width |
| `Ctrl+w <` | Decrease width |
| `Ctrl+w =` | Equal size |
| `Ctrl+w _` | Maximize height |
| `Ctrl+w |` | Maximize width |

### Close Windows
| Key | Action |
|-----|--------|
| `Ctrl+w q` | Close window |
| `Ctrl+w c` | Close window |
| `Ctrl+w o` | Close all except current |
| `:only` | Close all except current |

### Tabs
| Command | Action |
|---------|--------|
| `:tabnew` | New tab |
| `:tabnew filename` | New tab with file |
| `:tabc` | Close tab |
| `:tabo` | Close all other tabs |
| `gt` | Next tab |
| `gT` | Previous tab |
| `{n}gt` | Go to tab n |
| `:tabs` | List tabs |

---

## Marks and Jumps

### Marks
| Key | Action |
|-----|--------|
| `m{a-z}` | Set local mark |
| `m{A-Z}` | Set global mark (across files) |
| `` `{mark}`` | Jump to mark (exact position) |
| `'{mark}` | Jump to mark (line start) |
| `:marks` | List all marks |
| `:delmarks a` | Delete mark a |
| `:delmarks!` | Delete all marks |

### Special Marks
| Mark | Position |
|------|----------|
| `` ` `` | Last jump position |
| `''` | Last jump position (line) |
| `` `. `` | Last edit position |
| `` `^ `` | Last insert position |
| `` `[ `` | Start of last change |
| `` `] `` | End of last change |
| `` `< `` | Start of last visual |
| `` `> `` | End of last visual |

### Jump List
| Key | Action |
|-----|--------|
| `Ctrl+o` | Jump back |
| `Ctrl+i` | Jump forward |
| `:jumps` | List jump history |

### Change List
| Key | Action |
|-----|--------|
| `g;` | Jump to previous change |
| `g,` | Jump to next change |
| `:changes` | List changes |

---

## Macros

### Record and Play
| Key | Action |
|-----|--------|
| `q{a-z}` | Start recording to register |
| `q` | Stop recording |
| `@{a-z}` | Play macro |
| `@@` | Replay last macro |
| `5@a` | Play macro 5 times |

### Example Workflow
```
qa          " Start recording to register 'a'
0           " Go to start of line
dwA,Esc    " Delete word, append comma
j           " Move down
q           " Stop recording

10@a        " Apply macro to next 10 lines
```

### Edit Macro
```vim
:let @a='...'      " Set macro content directly
"ap                " Paste macro content to edit
"ayy               " Yank edited macro back
```

---

## Registers

### Named Registers
| Register | Purpose |
|----------|---------|
| `"a` - `"z` | Named registers |
| `"A` - `"Z` | Append to named register |
| `""` | Unnamed (default) register |
| `"0` | Yank register (last yank) |
| `"1`-`"9` | Delete history |
| `"+` | System clipboard |
| `"*` | Selection clipboard (X11) |
| `"_` | Black hole (delete without saving) |
| `"/` | Last search pattern |
| `":` | Last command |
| `".` | Last inserted text |
| `"%` | Current filename |

### Using Registers
```
"ayy        " Yank line to register a
"ap         " Paste from register a
"+y         " Yank to system clipboard
"+p         " Paste from system clipboard
"_dd        " Delete line without saving
:reg        " View all registers
:reg ab     " View registers a and b
```

---

## Text Objects

Text objects allow precise selection/operation on code structures.

### Format: `{operator}{a|i}{object}`
- `a` = "a" (includes surrounding)
- `i` = "inner" (inside only)

### Common Text Objects
| Object | Description |
|--------|-------------|
| `w` | Word |
| `W` | WORD |
| `s` | Sentence |
| `p` | Paragraph |
| `t` | Tag (HTML/XML) |
| `"` | Double quotes |
| `'` | Single quotes |
| `` ` `` | Backticks |
| `(` or `)` | Parentheses |
| `[` or `]` | Brackets |
| `{` or `}` | Braces |
| `<` or `>` | Angle brackets |

### Examples
```
ciw         " Change inner word
daw         " Delete a word (with space)
ci"         " Change inside double quotes
da"         " Delete including quotes
cit         " Change inside HTML tag
yip         " Yank inner paragraph
vi{         " Select inside braces
va(         " Select including parentheses
```

---

## Folding

### Fold Commands
| Key | Action |
|-----|--------|
| `zf{motion}` | Create fold |
| `zo` | Open fold |
| `zc` | Close fold |
| `za` | Toggle fold |
| `zR` | Open all folds |
| `zM` | Close all folds |
| `zr` | Reduce fold level |
| `zm` | Increase fold level |
| `zd` | Delete fold |
| `zE` | Delete all folds |
| `zj` | Move to next fold |
| `zk` | Move to previous fold |

### Fold Methods
```vim
:set foldmethod=manual    " Manual folding
:set foldmethod=indent    " Based on indentation
:set foldmethod=syntax    " Based on syntax
:set foldmethod=marker    " Based on markers {{{ }}}
:set foldmethod=expr      " Custom expression
```

---

## Command Mode

### Useful Commands
```vim
:!command           " Run shell command
:r !command         " Insert command output
:w !command         " Send buffer to command
:terminal           " Open terminal (Neovim/Vim 8+)
:sort               " Sort lines
:sort!              " Sort reverse
:sort u             " Sort unique
:sort n             " Sort numerically
:g/pattern/d        " Delete lines matching pattern
:v/pattern/d        " Delete lines NOT matching pattern
:g/pattern/normal @a " Run macro on matching lines
:%normal A;         " Append ; to all lines
:%!jq .             " Format JSON with jq
```

### Range Specifiers
```vim
:5,10               " Lines 5 to 10
:%                  " Entire file
:.                  " Current line
:$                  " Last line
:'<,'>              " Visual selection
:.,+5               " Current line and next 5
:g/pattern/         " Lines matching pattern
```

---

## Configuration

### Basic vimrc/init.vim
```vim
" General
set number              " Line numbers
set relativenumber      " Relative line numbers
set cursorline          " Highlight current line
set wrap                " Wrap long lines
set linebreak           " Wrap at word boundaries

" Search
set ignorecase          " Case insensitive search
set smartcase           " Case sensitive if uppercase
set hlsearch            " Highlight search results
set incsearch           " Incremental search

" Indentation
set tabstop=4           " Tab width
set shiftwidth=4        " Indent width
set expandtab           " Spaces instead of tabs
set autoindent          " Auto-indent new lines
set smartindent         " Smart indentation

" Interface
set mouse=a             " Enable mouse
set scrolloff=8         " Keep 8 lines above/below cursor
set sidescrolloff=8     " Keep 8 columns left/right
set splitright          " New vertical splits on right
set splitbelow          " New horizontal splits below

" Files
set autoread            " Reload changed files
set nobackup            " No backup files
set noswapfile          " No swap files
set undofile            " Persistent undo

" Clipboard
set clipboard=unnamed   " Use system clipboard
set clipboard+=unnamedplus " Linux clipboard
```

### Key Mappings
```vim
" Leader key
let mapleader = " "     " Space as leader

" Quick save
nnoremap <leader>w :w<CR>

" Quick quit
nnoremap <leader>q :q<CR>

" Clear search highlight
nnoremap <leader>n :nohlsearch<CR>

" Better window navigation
nnoremap <C-h> <C-w>h
nnoremap <C-j> <C-w>j
nnoremap <C-k> <C-w>k
nnoremap <C-l> <C-w>l

" Move lines up/down
vnoremap J :m '>+1<CR>gv=gv
vnoremap K :m '<-2<CR>gv=gv

" Stay in visual mode when indenting
vnoremap < <gv
vnoremap > >gv
```

---

## Neovim Specific

### Neovim Features
```vim
" Built-in LSP
:LspInfo                " LSP status
:LspStart               " Start LSP
:LspStop                " Stop LSP

" Built-in terminal
:terminal               " Open terminal
<C-\><C-n>              " Exit terminal mode

" Built-in file explorer
:Explore                " Open netrw
:Lexplore               " Open in sidebar
```

### Lua Configuration (init.lua)
```lua
-- Basic options
vim.opt.number = true
vim.opt.relativenumber = true
vim.opt.tabstop = 4
vim.opt.shiftwidth = 4
vim.opt.expandtab = true

-- Key mappings
vim.g.mapleader = " "
vim.keymap.set('n', '<leader>w', ':w<CR>')
vim.keymap.set('n', '<leader>q', ':q<CR>')

-- Clipboard
vim.opt.clipboard = 'unnamedplus'
```

---

## Plugins

### Popular Plugin Managers
```bash
# vim-plug
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim

# Neovim: lazy.nvim (recommended)
# Neovim: packer.nvim
```

### Essential Plugins
| Plugin | Purpose |
|--------|---------|
| telescope.nvim | Fuzzy finder (Neovim) |
| fzf.vim | Fuzzy finder (Vim) |
| nvim-treesitter | Syntax highlighting |
| nvim-lspconfig | LSP configuration |
| nvim-cmp | Autocompletion |
| lualine.nvim | Status line |
| nvim-tree.lua | File explorer |
| gitsigns.nvim | Git integration |
| which-key.nvim | Keybinding hints |
| Comment.nvim | Easy commenting |

### vim-plug Example
```vim
call plug#begin()
Plug 'tpope/vim-sensible'
Plug 'tpope/vim-surround'
Plug 'tpope/vim-commentary'
Plug 'junegunn/fzf.vim'
Plug 'preservim/nerdtree'
call plug#end()
```

---

## Quick Reference Card

### Survival Commands
| Key | Action |
|-----|--------|
| `i` | Insert mode |
| `Esc` | Normal mode |
| `:w` | Save |
| `:q` | Quit |
| `:wq` | Save and quit |
| `u` | Undo |
| `.` | Repeat |

### Movement Essentials
| Key | Action |
|-----|--------|
| `hjkl` | Left/Down/Up/Right |
| `w/b` | Word forward/back |
| `0/$` | Line start/end |
| `gg/G` | File start/end |
| `Ctrl+d/u` | Half page down/up |

### Editing Essentials
| Key | Action |
|-----|--------|
| `x` | Delete char |
| `dd` | Delete line |
| `yy` | Copy line |
| `p` | Paste |
| `ciw` | Change word |
| `>>` | Indent |

### Common Patterns
```
ci"         Change inside quotes
dap         Delete paragraph
yip         Copy paragraph
=G          Auto-indent to end
gg=G        Auto-indent file
:%s/a/b/g   Replace all a with b
```

---

*Pro tip: Start with basic navigation (hjkl, w, b, gg, G), then learn operators (d, c, y) + motions. Master one new thing each week.*
