# Shell Scripting Guide

Practical guide to writing Bash/Zsh scripts. From basics to best practices.

## Table of Contents
- [Getting Started](#getting-started)
- [Variables](#variables)
- [Conditionals](#conditionals)
- [Loops](#loops)
- [Functions](#functions)
- [Input & Arguments](#input--arguments)
- [String Operations](#string-operations)
- [Arrays](#arrays)
- [File Operations](#file-operations)
- [Error Handling](#error-handling)
- [Debugging](#debugging)
- [Best Practices](#best-practices)
- [Useful Patterns](#useful-patterns)
- [Script Templates](#script-templates)

---

## Getting Started

### Create a script
```bash
touch myscript.sh
chmod +x myscript.sh
```

### Shebang (first line)
```bash
#!/bin/bash                 # Use bash
#!/bin/zsh                  # Use zsh
#!/usr/bin/env bash         # Portable - finds bash in PATH
#!/usr/bin/env zsh          # Portable - finds zsh in PATH
```

### Run a script
```bash
./myscript.sh               # Execute directly (needs chmod +x)
bash myscript.sh            # Run with bash
source myscript.sh          # Run in current shell (affects current env)
. myscript.sh               # Same as source
```

### Basic script structure
```bash
#!/usr/bin/env bash

# Script description
# Author: Your Name
# Date: 2024-01-01

set -euo pipefail           # Strict mode (recommended)

# Your code here
echo "Hello, World!"
```

---

## Variables

### Declaring variables
```bash
name="John"                 # No spaces around =
age=25
readonly PI=3.14159         # Constant (can't be changed)
```

### Using variables
```bash
echo $name                  # Simple usage
echo "$name"                # Preferred (quoted)
echo "${name}"              # Explicit (best for concatenation)
echo "Hello, ${name}!"      # String interpolation
echo 'Hello, $name'         # Single quotes = literal (no expansion)
```

### Default values
```bash
# Use default if variable is unset or empty
echo "${name:-default}"     # Returns "default" if $name is unset/empty

# Set default if variable is unset or empty
echo "${name:=default}"     # Sets $name to "default" if unset/empty

# Use alternative if variable IS set
echo "${name:+alternative}" # Returns "alternative" if $name is set

# Error if unset
echo "${name:?Error message}" # Exits with error if $name is unset
```

### Variable types
```bash
# String
greeting="Hello World"

# Integer (for arithmetic)
count=10
((count++))                 # Increment
((count += 5))              # Add 5

# Array
fruits=("apple" "banana" "cherry")

# Associative array (bash 4+)
declare -A colors
colors[red]="#FF0000"
colors[green]="#00FF00"
```

### Special variables
```bash
$0                          # Script name
$1, $2, ...                 # Positional arguments
$#                          # Number of arguments
$@                          # All arguments (as separate strings)
$*                          # All arguments (as single string)
$$                          # Current process ID
$?                          # Exit status of last command
$!                          # PID of last background process
$_                          # Last argument of previous command
```

### Environment variables
```bash
export MY_VAR="value"       # Export to child processes
unset MY_VAR                # Remove variable

# Common environment variables
echo $HOME                  # Home directory
echo $USER                  # Current username
echo $PWD                   # Current directory
echo $PATH                  # Executable search path
echo $SHELL                 # Current shell
```

---

## Conditionals

### If statement
```bash
if [[ condition ]]; then
    # code
elif [[ condition ]]; then
    # code
else
    # code
fi
```

### String comparisons
```bash
[[ "$a" == "$b" ]]          # Equal
[[ "$a" != "$b" ]]          # Not equal
[[ "$a" < "$b" ]]           # Less than (alphabetically)
[[ "$a" > "$b" ]]           # Greater than
[[ -z "$a" ]]               # Empty string
[[ -n "$a" ]]               # Non-empty string
[[ "$a" =~ pattern ]]       # Regex match
```

### Numeric comparisons
```bash
[[ $a -eq $b ]]             # Equal
[[ $a -ne $b ]]             # Not equal
[[ $a -lt $b ]]             # Less than
[[ $a -le $b ]]             # Less than or equal
[[ $a -gt $b ]]             # Greater than
[[ $a -ge $b ]]             # Greater than or equal

# Alternative with (( ))
(( a == b ))
(( a < b ))
(( a >= b ))
```

### File tests
```bash
[[ -e "$file" ]]            # File exists
[[ -f "$file" ]]            # Is regular file
[[ -d "$dir" ]]             # Is directory
[[ -r "$file" ]]            # Is readable
[[ -w "$file" ]]            # Is writable
[[ -x "$file" ]]            # Is executable
[[ -s "$file" ]]            # File is not empty
[[ -L "$file" ]]            # Is symbolic link
[[ "$f1" -nt "$f2" ]]       # f1 is newer than f2
[[ "$f1" -ot "$f2" ]]       # f1 is older than f2
```

### Logical operators
```bash
[[ cond1 && cond2 ]]        # AND
[[ cond1 || cond2 ]]        # OR
[[ ! condition ]]           # NOT

# Alternative
[[ cond1 ]] && [[ cond2 ]]
[[ cond1 ]] || [[ cond2 ]]
```

### Case statement
```bash
case "$variable" in
    pattern1)
        # code
        ;;
    pattern2|pattern3)
        # code for either pattern
        ;;
    *)
        # default case
        ;;
esac
```

### Examples
```bash
# Check if file exists
if [[ -f "$file" ]]; then
    echo "File exists"
else
    echo "File not found"
fi

# Check command success
if command -v git &>/dev/null; then
    echo "Git is installed"
fi

# Multiple conditions
if [[ -f "$file" && -r "$file" ]]; then
    cat "$file"
fi

# Case example
case "$1" in
    start)
        start_service
        ;;
    stop)
        stop_service
        ;;
    restart)
        stop_service
        start_service
        ;;
    *)
        echo "Usage: $0 {start|stop|restart}"
        exit 1
        ;;
esac
```

---

## Loops

### For loop
```bash
# Loop through list
for item in apple banana cherry; do
    echo "$item"
done

# Loop through array
fruits=("apple" "banana" "cherry")
for fruit in "${fruits[@]}"; do
    echo "$fruit"
done

# Loop through files
for file in *.txt; do
    echo "Processing $file"
done

# C-style for loop
for ((i=0; i<10; i++)); do
    echo "$i"
done

# Loop through range
for i in {1..10}; do
    echo "$i"
done

# Range with step
for i in {0..20..5}; do
    echo "$i"    # 0, 5, 10, 15, 20
done

# Loop through command output
for user in $(cat users.txt); do
    echo "User: $user"
done
```

### While loop
```bash
# Basic while
count=0
while [[ $count -lt 5 ]]; do
    echo "$count"
    ((count++))
done

# Read file line by line
while IFS= read -r line; do
    echo "$line"
done < file.txt

# Infinite loop
while true; do
    echo "Running..."
    sleep 1
done

# Read with timeout
while read -t 5 -r input; do
    echo "You entered: $input"
done
```

### Until loop
```bash
# Run until condition is true
count=0
until [[ $count -ge 5 ]]; do
    echo "$count"
    ((count++))
done
```

### Loop control
```bash
# Break - exit loop
for i in {1..10}; do
    if [[ $i -eq 5 ]]; then
        break
    fi
    echo "$i"
done

# Continue - skip to next iteration
for i in {1..10}; do
    if [[ $((i % 2)) -eq 0 ]]; then
        continue    # Skip even numbers
    fi
    echo "$i"
done
```

### Select (menu)
```bash
options=("Option 1" "Option 2" "Quit")
select choice in "${options[@]}"; do
    case $choice in
        "Option 1")
            echo "You chose option 1"
            ;;
        "Option 2")
            echo "You chose option 2"
            ;;
        "Quit")
            break
            ;;
        *)
            echo "Invalid option"
            ;;
    esac
done
```

---

## Functions

### Define functions
```bash
# Method 1 (preferred)
my_function() {
    echo "Hello from function"
}

# Method 2
function my_function {
    echo "Hello from function"
}
```

### Function with arguments
```bash
greet() {
    local name="$1"         # Local variable
    local greeting="${2:-Hello}"  # With default
    echo "$greeting, $name!"
}

greet "John"                # Hello, John!
greet "John" "Hi"           # Hi, John!
```

### Return values
```bash
# Return exit status (0-255)
is_even() {
    local num=$1
    if (( num % 2 == 0 )); then
        return 0            # Success/true
    else
        return 1            # Failure/false
    fi
}

if is_even 4; then
    echo "Even"
fi

# Return string via echo
get_greeting() {
    local name="$1"
    echo "Hello, $name"
}

result=$(get_greeting "John")
echo "$result"
```

### Local variables
```bash
my_function() {
    local var="local value"  # Only exists in function
    global_var="global"      # Affects parent scope
}
```

### Function examples
```bash
# Check if command exists
command_exists() {
    command -v "$1" &>/dev/null
}

if command_exists git; then
    echo "Git is installed"
fi

# Logging function
log() {
    local level="$1"
    shift
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] [$level] $*"
}

log INFO "Script started"
log ERROR "Something went wrong"

# Cleanup function
cleanup() {
    rm -f "$temp_file"
    echo "Cleaned up"
}
trap cleanup EXIT           # Run cleanup on exit
```

---

## Input & Arguments

### Command line arguments
```bash
#!/bin/bash
echo "Script: $0"
echo "First arg: $1"
echo "Second arg: $2"
echo "All args: $@"
echo "Number of args: $#"

# Shift arguments
shift                       # $2 becomes $1, etc.
```

### Parse options with getopts
```bash
#!/bin/bash
while getopts "hv:f:" opt; do
    case $opt in
        h)
            echo "Usage: $0 [-h] [-v value] [-f file]"
            exit 0
            ;;
        v)
            value="$OPTARG"
            ;;
        f)
            file="$OPTARG"
            ;;
        \?)
            echo "Invalid option: -$OPTARG"
            exit 1
            ;;
    esac
done

# Remaining args after options
shift $((OPTIND - 1))
echo "Remaining args: $@"
```

### Long options parsing
```bash
#!/bin/bash
while [[ $# -gt 0 ]]; do
    case $1 in
        -h|--help)
            echo "Usage: $0 [options]"
            exit 0
            ;;
        -v|--verbose)
            verbose=true
            shift
            ;;
        -f|--file)
            file="$2"
            shift 2
            ;;
        -*)
            echo "Unknown option: $1"
            exit 1
            ;;
        *)
            args+=("$1")
            shift
            ;;
    esac
done
```

### User input
```bash
# Read single line
read -p "Enter your name: " name
echo "Hello, $name"

# Read with timeout
read -t 5 -p "Quick! Enter something: " answer

# Read password (no echo)
read -s -p "Password: " password
echo ""

# Read single character
read -n 1 -p "Continue? [y/n] " answer
echo ""

# Read into array
read -a fruits -p "Enter fruits (space-separated): "
echo "First fruit: ${fruits[0]}"
```

### Validate input
```bash
# Require non-empty
while [[ -z "$name" ]]; do
    read -p "Enter name (required): " name
done

# Validate number
while ! [[ "$age" =~ ^[0-9]+$ ]]; do
    read -p "Enter age (numbers only): " age
done

# Yes/No confirmation
read -p "Are you sure? [y/N] " confirm
if [[ "$confirm" =~ ^[Yy]$ ]]; then
    echo "Proceeding..."
else
    echo "Cancelled"
    exit 1
fi
```

---

## String Operations

### String length
```bash
str="Hello World"
echo ${#str}                # 11
```

### Substring
```bash
str="Hello World"
echo ${str:0:5}             # Hello (from 0, length 5)
echo ${str:6}               # World (from position 6)
echo ${str: -5}             # World (last 5 chars, note space)
```

### Search and replace
```bash
str="hello world world"
echo ${str/world/universe}  # hello universe world (first)
echo ${str//world/universe} # hello universe universe (all)
echo ${str/#hello/hi}       # hi world world (prefix)
echo ${str/%world/universe} # hello world universe (suffix)
```

### Remove patterns
```bash
file="/path/to/file.txt"
echo ${file#*/}             # path/to/file.txt (remove shortest from start)
echo ${file##*/}            # file.txt (remove longest from start)
echo ${file%/*}             # /path/to (remove shortest from end)
echo ${file%%/*}            # (empty - remove longest from end)
echo ${file%.txt}           # /path/to/file (remove extension)
```

### Case conversion (bash 4+)
```bash
str="Hello World"
echo ${str^^}               # HELLO WORLD (uppercase)
echo ${str,,}               # hello world (lowercase)
echo ${str^}                # Hello world (capitalize first)
```

### String comparison
```bash
[[ "$str" == "hello" ]]     # Exact match
[[ "$str" == hello* ]]      # Starts with "hello"
[[ "$str" == *world ]]      # Ends with "world"
[[ "$str" =~ ^[0-9]+$ ]]    # Regex match (numbers only)
```

### Split string
```bash
str="a,b,c,d"
IFS=',' read -ra arr <<< "$str"
for item in "${arr[@]}"; do
    echo "$item"
done
```

### Join array
```bash
arr=("a" "b" "c")
IFS=','; echo "${arr[*]}"   # a,b,c
```

---

## Arrays

### Indexed arrays
```bash
# Declare
fruits=("apple" "banana" "cherry")
fruits[3]="date"            # Add element

# Access
echo ${fruits[0]}           # First element
echo ${fruits[-1]}          # Last element (bash 4.3+)
echo ${fruits[@]}           # All elements
echo ${fruits[*]}           # All elements (as single string)
echo ${#fruits[@]}          # Array length
echo ${!fruits[@]}          # All indices

# Slice
echo ${fruits[@]:1:2}       # Elements 1-2
```

### Associative arrays (bash 4+)
```bash
declare -A user
user[name]="John"
user[age]=25
user[email]="john@example.com"

echo ${user[name]}          # John
echo ${!user[@]}            # All keys
echo ${user[@]}             # All values

# Check if key exists
if [[ -v user[name] ]]; then
    echo "Key exists"
fi
```

### Array operations
```bash
# Append
fruits+=("elderberry")

# Remove element
unset 'fruits[1]'           # Remove by index

# Copy array
new_array=("${fruits[@]}")

# Concatenate arrays
combined=("${arr1[@]}" "${arr2[@]}")

# Loop through array
for fruit in "${fruits[@]}"; do
    echo "$fruit"
done

# Loop with index
for i in "${!fruits[@]}"; do
    echo "$i: ${fruits[$i]}"
done
```

### Array from command output
```bash
# Lines to array
readarray -t lines < file.txt
mapfile -t lines < file.txt     # Same as readarray

# Command output to array
files=($(ls *.txt))             # Don't use - breaks on spaces
readarray -t files < <(ls *.txt)  # Better
```

---

## File Operations

### Read file
```bash
# Entire file to variable
content=$(cat file.txt)
content=$(<file.txt)            # Faster, no subprocess

# Line by line
while IFS= read -r line; do
    echo "$line"
done < file.txt

# With line numbers
line_num=0
while IFS= read -r line; do
    ((line_num++))
    echo "$line_num: $line"
done < file.txt
```

### Write file
```bash
# Overwrite
echo "content" > file.txt

# Append
echo "more content" >> file.txt

# Multi-line (heredoc)
cat > file.txt << 'EOF'
Line 1
Line 2
Line 3
EOF

# Variables in heredoc (remove quotes around EOF)
cat > file.txt << EOF
Hello, $name
Today is $(date)
EOF
```

### File paths
```bash
file="/path/to/file.txt"

# Get directory
dirname "$file"             # /path/to

# Get filename
basename "$file"            # file.txt

# Get filename without extension
basename "$file" .txt       # file

# Using parameter expansion
echo "${file%/*}"           # /path/to (directory)
echo "${file##*/}"          # file.txt (filename)
echo "${file##*.}"          # txt (extension)
```

### Temp files
```bash
# Create temp file
temp_file=$(mktemp)
echo "Temp: $temp_file"

# Create temp directory
temp_dir=$(mktemp -d)
echo "Temp dir: $temp_dir"

# Cleanup on exit
trap 'rm -rf "$temp_file" "$temp_dir"' EXIT
```

---

## Error Handling

### Exit codes
```bash
# Success
exit 0

# Error
exit 1

# Check last command status
if [[ $? -ne 0 ]]; then
    echo "Command failed"
fi

# Run command and check
if ! command; then
    echo "Command failed"
    exit 1
fi
```

### Strict mode
```bash
#!/bin/bash
set -e          # Exit on error
set -u          # Error on undefined variable
set -o pipefail # Error on pipe failure
set -x          # Debug mode (print commands)

# Combined
set -euo pipefail

# Disable temporarily
set +e          # Allow errors
risky_command
set -e          # Re-enable
```

### Error handling patterns
```bash
# Die function
die() {
    echo "Error: $*" >&2
    exit 1
}

[[ -f "$file" ]] || die "File not found: $file"

# Try/catch pattern
if ! output=$(command 2>&1); then
    echo "Failed: $output"
    exit 1
fi

# Trap errors
trap 'echo "Error on line $LINENO"; exit 1' ERR

# Custom error handler
error_handler() {
    local line_no=$1
    local error_code=$2
    echo "Error on line $line_no (code: $error_code)"
}
trap 'error_handler $LINENO $?' ERR
```

### Cleanup on exit
```bash
cleanup() {
    rm -f "$temp_file"
    echo "Cleanup complete"
}
trap cleanup EXIT           # Always runs on exit
trap cleanup INT TERM       # On Ctrl+C or kill
```

---

## Debugging

### Debug options
```bash
#!/bin/bash -x              # Debug mode in shebang

set -x                      # Enable debug output
set +x                      # Disable debug output

# Debug specific section
set -x
problematic_code
set +x
```

### Print statements
```bash
echo "DEBUG: variable = $variable" >&2

# Verbose mode
[[ $verbose == true ]] && echo "Processing $file..."
```

### Trace execution
```bash
# Show line numbers
PS4='+ ${BASH_SOURCE}:${LINENO}: '
set -x
```

### Check syntax
```bash
bash -n script.sh           # Syntax check only
shellcheck script.sh        # Lint tool (install: brew install shellcheck)
```

---

## Best Practices

### Always quote variables
```bash
# Bad
echo $variable
rm -rf $dir/*

# Good
echo "$variable"
rm -rf "${dir:?}"/*         # :? prevents empty var from deleting /
```

### Use [[ ]] instead of [ ]
```bash
# Bad (POSIX but limited)
[ "$a" = "$b" ]

# Good (bash/zsh extended)
[[ "$a" == "$b" ]]
```

### Check if running as root
```bash
if [[ $EUID -ne 0 ]]; then
    echo "This script must be run as root"
    exit 1
fi
```

### Validate inputs
```bash
# Required argument
if [[ -z "${1:-}" ]]; then
    echo "Usage: $0 <filename>"
    exit 1
fi

# File exists
if [[ ! -f "$1" ]]; then
    echo "File not found: $1"
    exit 1
fi
```

### Use functions
```bash
# Break into small, focused functions
main() {
    validate_args "$@"
    setup_environment
    do_work
    cleanup
}

main "$@"
```

### Use shellcheck
```bash
# Install
brew install shellcheck     # macOS
apt install shellcheck      # Ubuntu

# Run
shellcheck script.sh
```

---

## Useful Patterns

### Require root
```bash
require_root() {
    if [[ $EUID -ne 0 ]]; then
        echo "Please run as root"
        exit 1
    fi
}
```

### Require command
```bash
require_command() {
    if ! command -v "$1" &>/dev/null; then
        echo "Required command not found: $1"
        exit 1
    fi
}
require_command git
require_command docker
```

### Retry logic
```bash
retry() {
    local max_attempts=$1
    local delay=$2
    shift 2
    local attempt=1

    until "$@"; do
        if (( attempt >= max_attempts )); then
            echo "Command failed after $max_attempts attempts"
            return 1
        fi
        echo "Attempt $attempt failed. Retrying in ${delay}s..."
        ((attempt++))
        sleep "$delay"
    done
}

retry 3 5 curl -f https://example.com
```

### Progress indicator
```bash
spin() {
    local chars="/-\|"
    while :; do
        for (( i=0; i<${#chars}; i++ )); do
            printf "\r%s" "${chars:$i:1}"
            sleep 0.1
        done
    done
}

# Use in background
spin &
spin_pid=$!
long_running_command
kill $spin_pid 2>/dev/null
printf "\rDone!\n"
```

### Logging
```bash
readonly LOG_FILE="/var/log/myscript.log"

log() {
    local level=$1
    shift
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] [$level] $*" | tee -a "$LOG_FILE"
}

log INFO "Starting script"
log WARN "Something might be wrong"
log ERROR "Something is definitely wrong"
```

### Lock file (prevent concurrent runs)
```bash
readonly LOCK_FILE="/tmp/myscript.lock"

acquire_lock() {
    if ! mkdir "$LOCK_FILE" 2>/dev/null; then
        echo "Script is already running"
        exit 1
    fi
    trap 'rm -rf "$LOCK_FILE"' EXIT
}

acquire_lock
```

---

## Script Templates

### Basic template
```bash
#!/usr/bin/env bash
#
# Script: myscript.sh
# Description: Brief description
# Author: Your Name
# Date: 2024-01-01

set -euo pipefail

# Constants
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly SCRIPT_NAME="$(basename "$0")"

# Functions
usage() {
    cat << EOF
Usage: $SCRIPT_NAME [options] <args>

Options:
    -h, --help      Show this help
    -v, --verbose   Verbose output

Examples:
    $SCRIPT_NAME file.txt
EOF
}

main() {
    # Parse arguments
    while [[ $# -gt 0 ]]; do
        case $1 in
            -h|--help)
                usage
                exit 0
                ;;
            -v|--verbose)
                verbose=true
                shift
                ;;
            *)
                args+=("$1")
                shift
                ;;
        esac
    done

    # Main logic here
    echo "Running $SCRIPT_NAME"
}

main "$@"
```

### CLI tool template
```bash
#!/usr/bin/env bash
set -euo pipefail

# Require bash 4+
if (( BASH_VERSINFO[0] < 4 )); then
    echo "Bash 4+ required"
    exit 1
fi

readonly VERSION="1.0.0"

# Colors
readonly RED='\033[0;31m'
readonly GREEN='\033[0;32m'
readonly YELLOW='\033[1;33m'
readonly NC='\033[0m'

log_info() { echo -e "${GREEN}[INFO]${NC} $*"; }
log_warn() { echo -e "${YELLOW}[WARN]${NC} $*"; }
log_error() { echo -e "${RED}[ERROR]${NC} $*" >&2; }

die() { log_error "$*"; exit 1; }

cmd_help() {
    cat << EOF
Usage: $(basename "$0") <command> [options]

Commands:
    start       Start the service
    stop        Stop the service
    status      Show status
    help        Show this help

Options:
    -v, --version   Show version
EOF
}

cmd_start() { log_info "Starting..."; }
cmd_stop() { log_info "Stopping..."; }
cmd_status() { log_info "Status: running"; }

main() {
    [[ $# -eq 0 ]] && { cmd_help; exit 0; }

    case $1 in
        -v|--version) echo "v$VERSION" ;;
        start) cmd_start ;;
        stop) cmd_stop ;;
        status) cmd_status ;;
        help) cmd_help ;;
        *) die "Unknown command: $1" ;;
    esac
}

main "$@"
```

---

*Pro tip: Start simple, add complexity only when needed. Use shellcheck from the beginning.*
