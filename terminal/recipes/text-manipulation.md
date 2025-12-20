# Recipe: Text Manipulation

> Extract, transform, filter data from files and command output.

---

## Extract Columns

### Using cut

```bash
# First column (space-separated)
cut -d' ' -f1 file.txt

# Second column (comma-separated CSV)
cut -d',' -f2 data.csv

# Multiple columns
cut -d',' -f1,3,5 data.csv

# Range of columns
cut -d':' -f1-3 /etc/passwd

# From column N to end
cut -d',' -f3- data.csv
```

### Using awk

```bash
# First column (auto-splits on whitespace)
awk '{print $1}' file.txt

# Multiple columns
awk '{print $1, $3}' file.txt

# Custom delimiter
awk -F',' '{print $1}' data.csv

# Last column
awk '{print $NF}' file.txt

# Second to last
awk '{print $(NF-1)}' file.txt
```

---

## Filter Lines

### By pattern (grep)

```bash
# Lines containing pattern
grep "error" log.txt

# Lines NOT containing pattern
grep -v "debug" log.txt

# Lines starting with pattern
grep "^Error" log.txt

# Lines ending with pattern
grep "failed$" log.txt

# Multiple patterns (OR)
grep -E "error|warning|critical" log.txt
```

### By line number

```bash
# Specific line
sed -n '10p' file.txt

# Range of lines
sed -n '10,20p' file.txt

# First N lines
head -10 file.txt

# Last N lines
tail -10 file.txt

# All except first N
tail -n +5 file.txt

# All except last N
head -n -5 file.txt
```

### By content

```bash
# Lines longer than 80 chars
awk 'length > 80' file.txt

# Non-empty lines
grep -v '^$' file.txt

# Lines with specific number of fields
awk 'NF == 3' file.txt
```

---

## Replace Text

### In single file

```bash
# Replace first occurrence per line
sed 's/old/new/' file.txt

# Replace all occurrences
sed 's/old/new/g' file.txt

# In-place replacement
sed -i '' 's/old/new/g' file.txt      # macOS
sed -i 's/old/new/g' file.txt         # Linux

# Case-insensitive
sed 's/old/new/gi' file.txt
```

### In multiple files

```bash
# Find and replace across codebase
find . -name "*.js" -exec sed -i '' 's/oldFunc/newFunc/g' {} \;

# Preview first
grep -rl "oldFunc" --include="*.js" .

# Using perl (more powerful regex)
find . -name "*.py" -exec perl -pi -e 's/old_name/new_name/g' {} \;
```

### Common replacements

```bash
# Remove trailing whitespace
sed 's/[[:space:]]*$//' file.txt

# Remove blank lines
sed '/^$/d' file.txt

# Add prefix to each line
sed 's/^/prefix: /' file.txt

# Add suffix to each line
sed 's/$/ :suffix/' file.txt

# Replace newlines with commas
tr '\n' ',' < file.txt
```

---

## Sort and Unique

### Sorting

```bash
# Alphabetical sort
sort file.txt

# Numeric sort
sort -n numbers.txt

# Reverse sort
sort -r file.txt

# Sort by column (2nd column, numeric)
sort -k2 -n file.txt

# Sort CSV by 3rd column
sort -t',' -k3 data.csv

# Human-readable sizes
sort -h sizes.txt
```

### Remove duplicates

```bash
# Remove adjacent duplicates (after sorting)
sort file.txt | uniq

# Count occurrences
sort file.txt | uniq -c

# Show only duplicates
sort file.txt | uniq -d

# Show only unique lines
sort file.txt | uniq -u

# Remove duplicates preserving order
awk '!seen[$0]++' file.txt
```

### Top N patterns

```bash
# Most common lines
sort file.txt | uniq -c | sort -rn | head -10

# Most common words
cat file.txt | tr ' ' '\n' | sort | uniq -c | sort -rn | head -10

# Most common errors
grep "ERROR" log.txt | sort | uniq -c | sort -rn | head -10
```

---

## Transform Data

### Case conversion

```bash
# To uppercase
tr '[:lower:]' '[:upper:]' < file.txt

# To lowercase
tr '[:upper:]' '[:lower:]' < file.txt

# Capitalize each line (first char)
awk '{print toupper(substr($0,1,1)) tolower(substr($0,2))}' file.txt
```

### Character operations

```bash
# Delete specific characters
tr -d '[:digit:]' < file.txt

# Squeeze repeated characters
tr -s ' ' < file.txt

# Replace characters
tr 'abc' 'xyz' < file.txt

# Tabs to spaces
expand -t 4 file.txt
```

### Join and split

```bash
# Join all lines into one
paste -sd',' file.txt

# Join every 2 lines
paste -d' ' - - < file.txt

# Split by delimiter to lines
tr ',' '\n' < file.txt

# Merge files side by side
paste file1.txt file2.txt
```

---

## Calculate

### Sum numbers

```bash
# Sum all numbers (one per line)
awk '{sum+=$1} END {print sum}' numbers.txt

# Sum specific column
awk '{sum+=$3} END {print sum}' data.txt

# Sum with commas
awk -F',' '{sum+=$2} END {print sum}' data.csv
```

### Statistics

```bash
# Average
awk '{sum+=$1; count++} END {print sum/count}' numbers.txt

# Min and max
awk 'NR==1 {min=max=$1} {if($1<min)min=$1; if($1>max)max=$1} END {print "min:",min,"max:",max}' numbers.txt

# Count lines
wc -l file.txt
```

---

## JSON Processing

Requires `jq` (install: `brew install jq`)

```bash
# Pretty print
cat file.json | jq '.'

# Extract field
cat file.json | jq '.name'

# Raw string (no quotes)
cat file.json | jq -r '.name'

# Array element
cat file.json | jq '.users[0]'

# Filter array
cat file.json | jq '.users[] | select(.age > 25)'

# Extract multiple fields
cat file.json | jq '{name: .name, email: .email}'

# Get array length
cat file.json | jq '.items | length'
```

---

## Common Pipelines

```bash
# Unique IPs from access log, sorted by count
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head -10

# Extract emails from file
grep -oE '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-z]{2,}' file.txt

# Extract URLs
grep -oE 'https?://[^ ]+' file.txt

# Remove comments from config
grep -v '^#' config.txt | grep -v '^$'

# CSV to JSON (simple)
cat file.csv | python3 -c "import csv,json,sys; print(json.dumps(list(csv.DictReader(sys.stdin))))"
```

---

*For finding the files to manipulate, see [Find Things](find-things.md).*
