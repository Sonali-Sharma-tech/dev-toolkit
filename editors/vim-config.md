# Vim Configuration

> Make Vim feel like home.

Your vimrc is where you make Vim yours. Start minimal, add as you go.

## Table of Contents
- [Config File Locations](#config-file-locations)
- [Essential Settings](#essential-settings)
- [Key Mappings](#key-mappings)
- [My Recommended Setup](#my-recommended-setup)
- [Plugin Management](#plugin-management)
- [Common Customizations](#common-customizations)
- [Neovim-Specific Config](#neovim-specific-config)
- [Troubleshooting](#troubleshooting)

---

## Config File Locations

### Vim

```bash
~/.vimrc                    # Main config
~/.vim/                     # Vim directory
~/.vim/autoload/            # Plugins (vim-plug)
~/.vim/colors/              # Color schemes
```

### Neovim

```bash
~/.config/nvim/init.vim     # Vimscript config
~/.config/nvim/init.lua     # Lua config (recommended)
~/.config/nvim/lua/         # Lua modules
```

### Create if Missing

```bash
# Vim
touch ~/.vimrc

# Neovim
mkdir -p ~/.config/nvim
touch ~/.config/nvim/init.vim
# or for Lua
touch ~/.config/nvim/init.lua
```

---

## Essential Settings

Start with these. Understand each one.

```vim
" ─────────────────────────────────────────────
" ESSENTIALS - Add these to your vimrc
" ─────────────────────────────────────────────

" Don't be compatible with old vi
set nocompatible

" ─── Interface ───────────────────────────────

set number                  " Show line numbers
set relativenumber          " Relative line numbers (great for jumps)
set cursorline              " Highlight current line
set showmatch               " Highlight matching brackets
set signcolumn=yes          " Always show sign column (for git/lsp)
set colorcolumn=80          " Show column marker at 80 chars
set scrolloff=8             " Keep 8 lines visible above/below cursor
set sidescrolloff=8         " Keep 8 columns visible left/right

" ─── Search ──────────────────────────────────

set ignorecase              " Case insensitive search...
set smartcase               " ...unless you use capitals
set hlsearch                " Highlight search results
set incsearch               " Show matches as you type

" ─── Indentation ─────────────────────────────

set tabstop=4               " Tab = 4 spaces
set shiftwidth=4            " Indent = 4 spaces
set expandtab               " Use spaces, not tabs
set autoindent              " Copy indent from current line
set smartindent             " Smart indenting for code

" ─── Files ───────────────────────────────────

set autoread                " Reload files changed outside vim
set hidden                  " Allow hidden buffers
set noswapfile              " No swap files
set nobackup                " No backup files
set undofile                " Persistent undo
set undodir=~/.vim/undodir  " Undo file location

" ─── Behavior ────────────────────────────────

set mouse=a                 " Enable mouse
set clipboard=unnamed       " Use system clipboard
set clipboard+=unnamedplus  " Linux clipboard too
set splitright              " New vertical splits on right
set splitbelow              " New horizontal splits below
set updatetime=300          " Faster completion
set timeoutlen=500          " Key sequence timeout

" ─── Visual ──────────────────────────────────

syntax enable               " Syntax highlighting
set termguicolors           " True color support
set background=dark         " Dark theme
filetype plugin indent on   " File-type detection
```

### Why These Matter

| Setting | Why |
|---------|-----|
| `relativenumber` | Jump with `5j` or `10k` easily |
| `scrolloff=8` | Never lose context at top/bottom |
| `smartcase` | Search `/error` is case-insensitive, `/Error` is sensitive |
| `expandtab` | Prevents tab/space mixing headaches |
| `hidden` | Switch buffers without saving first |
| `undofile` | Undo history survives closing file |
| `clipboard=unnamed` | Yank/paste with system clipboard |

---

## Key Mappings

### Set Leader Key

```vim
" Space as leader key (highly recommended)
let mapleader = " "
let maplocalleader = " "
```

### Mapping Syntax

```vim
" Normal mode mapping
nnoremap <key> <action>

" Insert mode mapping
inoremap <key> <action>

" Visual mode mapping
vnoremap <key> <action>

" All modes
map <key> <action>

" Use noremap to prevent recursive mappings (always recommended)
```

### My Essential Mappings

```vim
" ─────────────────────────────────────────────
" KEY MAPPINGS
" ─────────────────────────────────────────────

" Quick save
nnoremap <leader>w :w<CR>

" Quick quit
nnoremap <leader>q :q<CR>

" Save and quit
nnoremap <leader>x :wq<CR>

" Clear search highlight
nnoremap <Esc> :noh<CR>

" Better window navigation
nnoremap <C-h> <C-w>h
nnoremap <C-j> <C-w>j
nnoremap <C-k> <C-w>k
nnoremap <C-l> <C-w>l

" Resize splits with arrows
nnoremap <C-Up> :resize +2<CR>
nnoremap <C-Down> :resize -2<CR>
nnoremap <C-Left> :vertical resize -2<CR>
nnoremap <C-Right> :vertical resize +2<CR>

" Move lines up/down in visual mode
vnoremap J :m '>+1<CR>gv=gv
vnoremap K :m '<-2<CR>gv=gv

" Stay in visual mode when indenting
vnoremap < <gv
vnoremap > >gv

" Keep cursor centered when scrolling
nnoremap <C-d> <C-d>zz
nnoremap <C-u> <C-u>zz

" Keep cursor centered when searching
nnoremap n nzzzv
nnoremap N Nzzzv

" Paste without losing clipboard content
xnoremap <leader>p "_dP

" Delete to black hole register
nnoremap <leader>d "_d
vnoremap <leader>d "_d

" Quick buffer navigation
nnoremap <leader>bn :bnext<CR>
nnoremap <leader>bp :bprevious<CR>
nnoremap <leader>bd :bdelete<CR>

" Open file explorer
nnoremap <leader>e :Explore<CR>

" Quick splits
nnoremap <leader>v :vsplit<CR>
nnoremap <leader>s :split<CR>
```

### Disable Arrow Keys (Train Yourself)

```vim
" Disable arrow keys in normal mode
nnoremap <Up> <Nop>
nnoremap <Down> <Nop>
nnoremap <Left> <Nop>
nnoremap <Right> <Nop>

" Disable arrow keys in insert mode
inoremap <Up> <Nop>
inoremap <Down> <Nop>
inoremap <Left> <Nop>
inoremap <Right> <Nop>
```

---

## My Recommended Setup

### Minimal vimrc (~50 lines)

```vim
" ═══════════════════════════════════════════════
" MINIMAL VIMRC - Copy this to get started
" ═══════════════════════════════════════════════

set nocompatible

" ─── Interface ─────────────────────────────────
set number relativenumber
set cursorline
set scrolloff=8
set signcolumn=yes
set colorcolumn=80

" ─── Search ────────────────────────────────────
set ignorecase smartcase
set hlsearch incsearch

" ─── Indentation ───────────────────────────────
set tabstop=4 shiftwidth=4 expandtab
set autoindent smartindent

" ─── Files ─────────────────────────────────────
set autoread hidden
set noswapfile nobackup
set undofile
set undodir=~/.vim/undodir

" ─── Behavior ──────────────────────────────────
set mouse=a
set clipboard=unnamed,unnamedplus
set splitright splitbelow
set updatetime=300

" ─── Visual ────────────────────────────────────
syntax enable
set termguicolors
set background=dark

" ─── Leader Key ────────────────────────────────
let mapleader = " "

" ─── Key Mappings ──────────────────────────────
nnoremap <leader>w :w<CR>
nnoremap <leader>q :q<CR>
nnoremap <Esc> :noh<CR>
nnoremap <C-h> <C-w>h
nnoremap <C-j> <C-w>j
nnoremap <C-k> <C-w>k
nnoremap <C-l> <C-w>l
vnoremap J :m '>+1<CR>gv=gv
vnoremap K :m '<-2<CR>gv=gv
vnoremap < <gv
vnoremap > >gv
nnoremap <C-d> <C-d>zz
nnoremap <C-u> <C-u>zz

" Create undo directory
if !isdirectory($HOME."/.vim/undodir")
    call mkdir($HOME."/.vim/undodir", "p")
endif
```

---

## Plugin Management

### vim-plug (Recommended for Vim)

#### Install vim-plug

```bash
# Vim
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim

# Neovim
curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

#### Configure Plugins

```vim
call plug#begin('~/.vim/plugged')

" Essential plugins
Plug 'tpope/vim-sensible'       " Sensible defaults
Plug 'tpope/vim-surround'       " Surround text objects
Plug 'tpope/vim-commentary'     " Comment stuff out
Plug 'tpope/vim-fugitive'       " Git integration

" File navigation
Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }
Plug 'junegunn/fzf.vim'         " Fuzzy finder

" Appearance
Plug 'morhetz/gruvbox'          " Color scheme
Plug 'vim-airline/vim-airline'  " Status line

call plug#end()

" After adding plugins, run:
" :PlugInstall
```

#### Plugin Commands

```vim
:PlugInstall    " Install plugins
:PlugUpdate     " Update plugins
:PlugClean      " Remove unused plugins
:PlugStatus     " Check status
```

### My Plugin Recommendations

| Plugin | Purpose | Priority |
|--------|---------|----------|
| `tpope/vim-surround` | `cs"'` changes `"` to `'` | Essential |
| `tpope/vim-commentary` | `gcc` toggles comment | Essential |
| `tpope/vim-fugitive` | Git integration | High |
| `junegunn/fzf.vim` | Fuzzy file finder | High |
| `airblade/vim-gitgutter` | Git diff in gutter | Medium |
| `preservim/nerdtree` | File explorer | Medium |

---

## Common Customizations

### Auto-Commands

```vim
" ─── Auto Commands ─────────────────────────────

" Remove trailing whitespace on save
autocmd BufWritePre * :%s/\s\+$//e

" Return to last edit position
autocmd BufReadPost *
    \ if line("'\"") > 1 && line("'\"") <= line("$") |
    \   exe "normal! g'\"" |
    \ endif

" Set filetype-specific settings
autocmd FileType python setlocal tabstop=4 shiftwidth=4
autocmd FileType javascript setlocal tabstop=2 shiftwidth=2
autocmd FileType yaml setlocal tabstop=2 shiftwidth=2

" Highlight yanked text briefly
autocmd TextYankPost * silent! lua vim.highlight.on_yank()
```

### Status Line (Without Plugin)

```vim
set laststatus=2
set statusline=
set statusline+=%#PmenuSel#
set statusline+=\ %f                    " File path
set statusline+=\ %m                    " Modified flag
set statusline+=%=                      " Right align
set statusline+=%#CursorColumn#
set statusline+=\ %y                    " File type
set statusline+=\ %{&fileencoding?&fileencoding:&encoding}
set statusline+=\ %l:%c                 " Line:column
set statusline+=\ %p%%\                 " Percentage
```

### Color Scheme

```vim
" Built-in schemes
colorscheme desert
colorscheme slate
colorscheme elflord

" With plugin (gruvbox)
Plug 'morhetz/gruvbox'
" ...
colorscheme gruvbox
let g:gruvbox_contrast_dark = 'hard'
```

### File Explorer Tweaks (netrw)

```vim
" Better netrw (built-in file explorer)
let g:netrw_banner = 0          " No banner
let g:netrw_liststyle = 3       " Tree view
let g:netrw_browse_split = 4    " Open in prior window
let g:netrw_winsize = 25        " 25% width
let g:netrw_altv = 1            " Open splits to right
```

---

## Neovim-Specific Config

### init.lua (Lua Configuration)

```lua
-- ═══════════════════════════════════════════════
-- NEOVIM INIT.LUA - Modern configuration
-- ═══════════════════════════════════════════════

-- ─── Options ──────────────────────────────────

local opt = vim.opt

opt.number = true
opt.relativenumber = true
opt.cursorline = true
opt.scrolloff = 8
opt.signcolumn = "yes"
opt.colorcolumn = "80"

opt.ignorecase = true
opt.smartcase = true
opt.hlsearch = true
opt.incsearch = true

opt.tabstop = 4
opt.shiftwidth = 4
opt.expandtab = true
opt.autoindent = true
opt.smartindent = true

opt.hidden = true
opt.swapfile = false
opt.backup = false
opt.undofile = true
opt.undodir = vim.fn.expand("~/.config/nvim/undodir")

opt.mouse = "a"
opt.clipboard = "unnamedplus"
opt.splitright = true
opt.splitbelow = true
opt.updatetime = 300
opt.termguicolors = true

-- ─── Leader Key ───────────────────────────────

vim.g.mapleader = " "
vim.g.maplocalleader = " "

-- ─── Key Mappings ─────────────────────────────

local keymap = vim.keymap.set
local opts = { noremap = true, silent = true }

-- Quick save/quit
keymap("n", "<leader>w", ":w<CR>", opts)
keymap("n", "<leader>q", ":q<CR>", opts)

-- Clear search highlight
keymap("n", "<Esc>", ":noh<CR>", opts)

-- Window navigation
keymap("n", "<C-h>", "<C-w>h", opts)
keymap("n", "<C-j>", "<C-w>j", opts)
keymap("n", "<C-k>", "<C-w>k", opts)
keymap("n", "<C-l>", "<C-w>l", opts)

-- Move lines
keymap("v", "J", ":m '>+1<CR>gv=gv", opts)
keymap("v", "K", ":m '<-2<CR>gv=gv", opts)

-- Stay in visual mode when indenting
keymap("v", "<", "<gv", opts)
keymap("v", ">", ">gv", opts)

-- Centered scrolling
keymap("n", "<C-d>", "<C-d>zz", opts)
keymap("n", "<C-u>", "<C-u>zz", opts)

-- Centered searching
keymap("n", "n", "nzzzv", opts)
keymap("n", "N", "Nzzzv", opts)

-- ─── Auto Commands ────────────────────────────

local augroup = vim.api.nvim_create_augroup
local autocmd = vim.api.nvim_create_autocmd

-- Highlight yanked text
augroup("YankHighlight", { clear = true })
autocmd("TextYankPost", {
    group = "YankHighlight",
    callback = function()
        vim.highlight.on_yank({ higroup = "IncSearch", timeout = 200 })
    end,
})

-- Remove trailing whitespace
autocmd("BufWritePre", {
    pattern = "*",
    command = ":%s/\\s\\+$//e",
})
```

### lazy.nvim (Modern Plugin Manager for Neovim)

```lua
-- Bootstrap lazy.nvim
local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
if not vim.loop.fs_stat(lazypath) then
    vim.fn.system({
        "git", "clone", "--filter=blob:none",
        "https://github.com/folke/lazy.nvim.git",
        "--branch=stable", lazypath,
    })
end
vim.opt.rtp:prepend(lazypath)

-- Plugins
require("lazy").setup({
    -- Essential
    "tpope/vim-surround",
    "tpope/vim-commentary",

    -- Fuzzy finder
    {
        "nvim-telescope/telescope.nvim",
        dependencies = { "nvim-lua/plenary.nvim" },
        keys = {
            { "<leader>ff", "<cmd>Telescope find_files<cr>" },
            { "<leader>fg", "<cmd>Telescope live_grep<cr>" },
            { "<leader>fb", "<cmd>Telescope buffers<cr>" },
        },
    },

    -- Color scheme
    {
        "folke/tokyonight.nvim",
        priority = 1000,
        config = function()
            vim.cmd.colorscheme("tokyonight")
        end,
    },

    -- Syntax highlighting
    {
        "nvim-treesitter/nvim-treesitter",
        build = ":TSUpdate",
    },
})
```

---

## Troubleshooting

### Problem: Settings Not Taking Effect

```bash
# Check if vimrc is being read
:echo $MYVIMRC

# Check current value of a setting
:set tabstop?

# See where setting was last changed
:verbose set tabstop?
```

### Problem: Clipboard Not Working

```bash
# Check clipboard support
vim --version | grep clipboard
# Look for +clipboard, not -clipboard

# Install vim with clipboard (macOS)
brew install vim

# Install vim with clipboard (Ubuntu)
sudo apt install vim-gtk3
```

### Problem: Colors Look Wrong

```vim
" Add to vimrc
set termguicolors

" If still wrong, check terminal supports true color
" In bash:
echo $TERM
# Should show something like xterm-256color
```

### Problem: Slow Startup

```bash
# Profile startup time
vim --startuptime startup.log

# Check what's slow
cat startup.log | sort -t: -k2 -n | tail -20
```

### Problem: Plugins Not Loading

```vim
" Check if plugin directory exists
:echo globpath(&rtp, 'plugin')

" Reload vimrc
:source $MYVIMRC

" Check for errors
:messages
```

---

## Migration Path

### Week 1: Survival
- Use the [Vim Survival Guide](vim-survival.md)
- Learn: `i`, `Esc`, `:wq`, `u`

### Week 2-3: Basic Config
- Add the minimal vimrc from this guide
- Learn: `hjkl`, `w/b`, `dd`, `yy`, `p`

### Week 4-5: Productivity
- Read [Vim Productivity](vim-productive.md)
- Learn: text objects (`ciw`, `da"`), visual mode

### Week 6+: Customize
- Add plugins as needed
- Build your own mappings
- Make it yours

---

*Your vimrc will grow with you. Start minimal, understand each line, add what you need.*
