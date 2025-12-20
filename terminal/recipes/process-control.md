# Recipe: Process Control

> Kill, monitor, and manage running processes.

---

## Find Processes

### By name

```bash
# Find process by name
ps aux | grep "node"

# Better: pgrep
pgrep -fl node

# Full process details
ps aux | grep "[n]ode"  # Brackets exclude grep itself
```

### By resource usage

```bash
# Top 10 memory hogs
ps aux --sort=-%mem | head -11

# Top 10 CPU hogs
ps aux --sort=-%cpu | head -11

# Interactive (real-time)
top                      # Basic
htop                     # Better (install: brew install htop)
```

### By port

```bash
# What's using port 3000?
lsof -i :3000

# Just the PID
lsof -ti :3000

# All listening ports
lsof -i -P -n | grep LISTEN      # macOS
ss -tulpn                         # Linux
netstat -tulpn                    # Linux (older)
```

---

## Kill Processes

### By PID

```bash
# Graceful kill (SIGTERM)
kill 1234

# Force kill (SIGKILL)
kill -9 1234

# List all signals
kill -l
```

### By name

```bash
# Kill all matching processes
pkill node

# Kill by pattern
pkill -f "node server.js"

# Kill all by exact name
killall node

# Interactive confirmation
pkill -i node
```

### By port

```bash
# Kill whatever's on port 3000
lsof -ti:3000 | xargs kill -9

# Or step by step
lsof -i :3000           # Find PID
kill -9 <PID>           # Kill it
```

### Common patterns

```bash
# Kill all background jobs
kill $(jobs -p)

# Kill all processes by user
pkill -u username

# Kill zombie processes (requires parent kill or reboot)
ps aux | grep -w defunct

# Kill unresponsive process
kill -9 $(pgrep -f "stuck_process")
```

---

## Background Jobs

### Run in background

```bash
# Start command in background
command &

# Start and detach from terminal
nohup command &

# Disown after starting
command &
disown %1
```

### Manage background jobs

```bash
# List jobs
jobs

# Bring to foreground
fg                    # Last job
fg %1                 # Job 1

# Send to background
bg                    # Last job
bg %2                 # Job 2

# Suspend current process
Ctrl + Z

# Kill job
kill %1
```

### Screen and Tmux

```bash
# Screen - keep process running after disconnect
screen -S myprocess   # Start named session
command               # Run your command
Ctrl+A, D             # Detach
screen -r myprocess   # Reattach

# Tmux - same concept
tmux new -s myprocess
command
Ctrl+B, D             # Detach
tmux attach -t myprocess
```

---

## Monitor Processes

### Real-time monitoring

```bash
# Watch specific command
watch -n 1 'ps aux | grep node'

# Watch command output
watch -n 2 'df -h'

# Monitor file changes
tail -f /var/log/app.log

# Monitor with filtering
tail -f log.txt | grep --line-buffered "ERROR"
```

### Resource monitoring

```bash
# System overview
top

# Better alternative
htop

# Memory usage
free -h                           # Linux
vm_stat                           # macOS

# CPU load
uptime

# Disk I/O
iostat 1                          # Every second
iotop                             # Per-process I/O (Linux)
```

### Per-process resources

```bash
# Memory of specific process
ps -o pid,vsz,rss,comm -p 1234

# CPU time of process
ps -o pid,%cpu,time,comm -p 1234

# Open files by process
lsof -p 1234

# Network connections by process
lsof -i -a -p 1234
```

---

## Signals

| Signal | Number | Meaning |
|--------|--------|---------|
| SIGHUP | 1 | Hangup (reload config) |
| SIGINT | 2 | Interrupt (Ctrl+C) |
| SIGKILL | 9 | Force kill (can't be caught) |
| SIGTERM | 15 | Graceful termination |
| SIGSTOP | 19 | Pause process |
| SIGCONT | 18 | Resume process |

```bash
# Graceful shutdown
kill -TERM 1234

# Force kill (last resort)
kill -KILL 1234
kill -9 1234

# Reload config without restart
kill -HUP 1234

# Pause and resume
kill -STOP 1234
kill -CONT 1234
```

---

## Timeouts and Limits

### Run with timeout

```bash
# Linux
timeout 10 ./slow_command

# macOS (install coreutils)
gtimeout 10 ./slow_command

# Any system
./command &
pid=$!
sleep 10
kill $pid 2>/dev/null
```

### Resource limits

```bash
# Show current limits
ulimit -a

# Max open files
ulimit -n

# Increase open files limit
ulimit -n 4096

# Run command with memory limit
ulimit -v 1000000 && command
```

---

## Process Priorities

```bash
# Run with lower priority
nice -n 10 command

# Run with higher priority (requires sudo)
sudo nice -n -10 command

# Change priority of running process
renice 10 -p 1234

# Check priority (NI column)
ps -l -p 1234
```

---

## Common Scenarios

### "My terminal is frozen"

```bash
# Try these in order:
Ctrl + C              # Cancel current command
Ctrl + Z              # Suspend process
Ctrl + Q              # Resume output (if Ctrl+S was pressed)
Ctrl + \              # Force quit (SIGQUIT)
```

### "Port already in use"

```bash
# Find what's using it
lsof -i :3000

# Kill it
lsof -ti:3000 | xargs kill -9
```

### "Process won't die"

```bash
# Escalate through signals
kill 1234             # Try graceful first
sleep 2
kill -9 1234          # Force if needed
```

### "Need to keep process running after logout"

```bash
# Option 1: nohup
nohup ./long_process &

# Option 2: screen
screen -S myprocess
./long_process
Ctrl+A, D

# Option 3: systemd (Linux, permanent)
sudo systemctl enable myservice
```

---

*For finding processes first, see [Find Things](find-things.md).*
