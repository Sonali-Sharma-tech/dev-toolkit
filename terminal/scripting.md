# Bash Scripting Essentials

> Write scripts that work. Just the patterns you actually need.

---

## Getting Started

### Create a script

```bash
touch myscript.sh
chmod +x myscript.sh
```

### Basic structure

```bash
#!/usr/bin/env bash
set -euo pipefail    # Exit on error, undefined vars, pipe failures

# Your code here
echo "Hello, World!"
```

### Run it

```bash
./myscript.sh        # Execute
bash myscript.sh     # Or run with bash
source myscript.sh   # Run in current shell
```

---

## Variables

```bash
# Set variables (no spaces around =)
name="John"
count=10

# Use variables (always quote)
echo "$name"
echo "${name}_suffix"

# Default values
echo "${name:-default}"      # Use "default" if unset
name="${name:-default}"      # Set to "default" if unset
```

### Special variables

```bash
$0          # Script name
$1, $2      # Arguments
$#          # Number of arguments
$@          # All arguments (as separate words)
$?          # Exit status of last command
$$          # Current process ID
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
[[ "$a" == "$b" ]]       # Equal
[[ "$a" != "$b" ]]       # Not equal
[[ -z "$a" ]]            # Empty
[[ -n "$a" ]]            # Not empty
```

### Number comparisons

```bash
[[ $a -eq $b ]]          # Equal
[[ $a -ne $b ]]          # Not equal
[[ $a -lt $b ]]          # Less than
[[ $a -gt $b ]]          # Greater than
```

### File tests

```bash
[[ -f "$file" ]]         # Is file
[[ -d "$dir" ]]          # Is directory
[[ -e "$path" ]]         # Exists
[[ -r "$file" ]]         # Is readable
[[ -w "$file" ]]         # Is writable
[[ -x "$file" ]]         # Is executable
```

### Logical operators

```bash
[[ cond1 && cond2 ]]     # AND
[[ cond1 || cond2 ]]     # OR
[[ ! condition ]]        # NOT
```

### Examples

```bash
# Check if file exists
if [[ -f "$file" ]]; then
    echo "File exists"
fi

# Check if command exists
if command -v git &>/dev/null; then
    echo "Git installed"
fi

# One-liner
[[ -f "$file" ]] && echo "exists" || echo "missing"
```

---

## Loops

### For loop

```bash
# Loop through items
for item in apple banana cherry; do
    echo "$item"
done

# Loop through files
for file in *.txt; do
    echo "Processing $file"
done

# C-style
for ((i=0; i<10; i++)); do
    echo "$i"
done

# Range
for i in {1..10}; do
    echo "$i"
done
```

### While loop

```bash
count=0
while [[ $count -lt 5 ]]; do
    echo "$count"
    ((count++))
done
```

### Read file line by line

```bash
while IFS= read -r line; do
    echo "$line"
done < file.txt
```

---

## Functions

```bash
# Define function
greet() {
    local name="$1"                    # Local variable
    local greeting="${2:-Hello}"       # With default
    echo "$greeting, $name!"
}

# Call function
greet "John"           # Hello, John!
greet "John" "Hi"      # Hi, John!
```

### Return values

```bash
# Return status (0 = success)
is_even() {
    (( $1 % 2 == 0 ))
}

if is_even 4; then
    echo "Even"
fi

# Return string via echo
get_name() {
    echo "John"
}

name=$(get_name)
```

---

## Arguments

### Basic usage

```bash
#!/usr/bin/env bash

echo "Script: $0"
echo "First arg: $1"
echo "All args: $@"
echo "Count: $#"
```

### With validation

```bash
# Require argument
if [[ -z "${1:-}" ]]; then
    echo "Usage: $0 <filename>"
    exit 1
fi

file="$1"
```

### Parse options

```bash
while [[ $# -gt 0 ]]; do
    case $1 in
        -h|--help)
            echo "Usage: $0 [-v] file"
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
```

---

## Error Handling

### Strict mode

```bash
#!/usr/bin/env bash
set -e          # Exit on error
set -u          # Error on undefined variable
set -o pipefail # Error if any pipe command fails

# Combined
set -euo pipefail
```

### Check command success

```bash
if ! command; then
    echo "Command failed"
    exit 1
fi
```

### Die function

```bash
die() {
    echo "Error: $*" >&2
    exit 1
}

[[ -f "$file" ]] || die "File not found: $file"
```

### Cleanup on exit

```bash
cleanup() {
    rm -f "$temp_file"
}
trap cleanup EXIT
```

---

## User Input

```bash
# Read input
read -p "Enter name: " name

# Password (hidden)
read -s -p "Password: " pass
echo ""

# With timeout
read -t 5 -p "Quick! " answer

# Yes/No confirmation
read -p "Continue? [y/N] " confirm
if [[ "$confirm" =~ ^[Yy]$ ]]; then
    echo "Proceeding..."
fi
```

---

## Common Patterns

### Check if root

```bash
if [[ $EUID -ne 0 ]]; then
    echo "Run as root"
    exit 1
fi
```

### Check if command exists

```bash
require_command() {
    command -v "$1" &>/dev/null || die "Required: $1"
}

require_command git
require_command docker
```

### Temp files

```bash
temp_file=$(mktemp)
trap 'rm -f "$temp_file"' EXIT
```

### Logging

```bash
log() {
    echo "[$(date '+%H:%M:%S')] $*"
}

log "Starting..."
```

### Retry

```bash
retry() {
    local max=$1
    shift
    for ((i=1; i<=max; i++)); do
        "$@" && return 0
        echo "Attempt $i failed"
        sleep 1
    done
    return 1
}

retry 3 curl -f https://example.com
```

---

## Script Template

```bash
#!/usr/bin/env bash
set -euo pipefail

# Constants
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"

# Functions
die() { echo "Error: $*" >&2; exit 1; }
log() { echo "[$(date '+%H:%M:%S')] $*"; }

usage() {
    cat << EOF
Usage: $(basename "$0") [options] <file>

Options:
    -h, --help     Show this help
    -v, --verbose  Verbose output
EOF
}

main() {
    local verbose=false

    while [[ $# -gt 0 ]]; do
        case $1 in
            -h|--help) usage; exit 0 ;;
            -v|--verbose) verbose=true; shift ;;
            *) break ;;
        esac
    done

    [[ $# -eq 0 ]] && die "Missing file argument"

    log "Processing $1..."
    # Your logic here
}

main "$@"
```

---

## Debugging

```bash
# Print commands as they run
set -x

# Or for specific section
set -x
problematic_code
set +x

# Syntax check only
bash -n script.sh

# Use shellcheck (install: brew install shellcheck)
shellcheck script.sh
```

---

*Pro tip: Always use shellcheck. It catches bugs before you run.*
