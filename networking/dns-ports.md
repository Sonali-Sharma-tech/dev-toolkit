# DNS & Ports

> Resolve names, find what's using ports, diagnose network issues.

## Table of Contents
- [DNS Lookup](#dns-lookup)
- [Port Management](#port-management)
- [Network Connections](#network-connections)
- [Common Scenarios](#common-scenarios)

---

## DNS Lookup

### Basic Lookup

```bash
dig example.com
```

### Short Answer Only

```bash
dig +short example.com
```

### Specific Record Types

```bash
dig A example.com        # IPv4 address
dig AAAA example.com     # IPv6 address
dig MX example.com       # Mail servers
dig TXT example.com      # TXT records
dig NS example.com       # Name servers
dig CNAME www.example.com # Canonical name
```

### Query Specific DNS Server

```bash
dig @8.8.8.8 example.com
dig @1.1.1.1 example.com
```

### Reverse Lookup (IP to Name)

```bash
dig -x 8.8.8.8
```

### Trace DNS Resolution

```bash
dig +trace example.com
```

Shows the full path from root servers.

### nslookup (Alternative)

```bash
nslookup example.com
nslookup -type=MX example.com
```

### Check DNS Propagation

```bash
# Query multiple DNS servers
for dns in 8.8.8.8 1.1.1.1 9.9.9.9; do
  echo "=== $dns ==="
  dig @$dns +short example.com
done
```

---

## Port Management

### What's Using a Port?

```bash
lsof -i :3000
```

### Kill Process on Port

```bash
lsof -ti :3000 | xargs kill -9
```

### Check if Port is Open (Local)

```bash
nc -zv localhost 3000
```

### Check if Port is Open (Remote)

```bash
nc -zv example.com 443
```

### List All Listening Ports

```bash
# macOS
lsof -i -P | grep LISTEN

# Linux
ss -tlnp
netstat -tlnp
```

### Find Process by Port (Detailed)

```bash
lsof -i :3000 -P -n
```

`-P` = show port numbers, `-n` = don't resolve hostnames

### Scan Port Range

```bash
nc -zv localhost 3000-3010
```

---

## Network Connections

### All Active Connections

```bash
# macOS
netstat -an | grep ESTABLISHED

# Linux
ss -t state established
```

### Connections to Specific Port

```bash
lsof -i :443
```

### Connections by Process

```bash
lsof -i -c nginx
```

### Count Connections by State

```bash
netstat -an | awk '/tcp/ {print $6}' | sort | uniq -c
```

### Your Network Interfaces

```bash
# macOS
ifconfig

# Linux
ip addr
```

### Your Local IP

```bash
# macOS
ipconfig getifaddr en0

# Linux
hostname -I
```

### Your Public IP

```bash
curl -s ifconfig.me
curl -s icanhazip.com
```

### Routing Table

```bash
netstat -rn
# or
ip route
```

---

## Common Scenarios

### Scenario: Port Already in Use

```bash
# Find what's using it
lsof -i :3000

# Output:
# node    12345 user   22u  IPv4  TCP *:3000 (LISTEN)

# Kill it
kill 12345

# Or force kill
kill -9 12345

# One-liner
lsof -ti :3000 | xargs kill -9
```

### Scenario: Can't Resolve Hostname

```bash
# Check if DNS is working
dig +short google.com

# If nothing, try different DNS
dig @8.8.8.8 +short google.com

# Flush DNS cache (macOS)
sudo dscacheutil -flushcache
sudo killall -HUP mDNSResponder

# Flush DNS cache (Linux)
sudo systemd-resolve --flush-caches
```

### Scenario: Check if Service is Reachable

```bash
# Step 1: Can you resolve the hostname?
dig +short api.example.com

# Step 2: Can you reach the port?
nc -zv api.example.com 443

# Step 3: Does HTTP work?
curl -I https://api.example.com
```

### Scenario: Find All Services on Network

```bash
# Scan local network (requires nmap)
nmap -sn 192.168.1.0/24

# Quick port scan
nmap -F 192.168.1.100
```

### Scenario: Test Connection Speed

```bash
# Ping with timing
ping -c 10 google.com

# Traceroute
traceroute google.com

# macOS alternative
mtr google.com
```

### Scenario: Hosts File Override

```bash
# Edit hosts file
sudo vim /etc/hosts

# Add line:
# 192.168.1.100  myapp.local
```

Hosts file takes precedence over DNS.

---

## Quick Reference

| Task | Command |
|------|---------|
| DNS lookup | `dig example.com` |
| Short DNS answer | `dig +short example.com` |
| MX records | `dig MX example.com` |
| What's on port? | `lsof -i :3000` |
| Kill port process | `lsof -ti :3000 \| xargs kill -9` |
| Is port open? | `nc -zv host port` |
| All listening | `lsof -i -P \| grep LISTEN` |
| Your local IP | `ipconfig getifaddr en0` |
| Your public IP | `curl -s ifconfig.me` |
| Flush DNS cache | `sudo dscacheutil -flushcache` |

---

## Common Ports

| Port | Service |
|------|---------|
| 22 | SSH |
| 80 | HTTP |
| 443 | HTTPS |
| 3000 | Node.js dev |
| 3306 | MySQL |
| 5432 | PostgreSQL |
| 6379 | Redis |
| 8080 | Alt HTTP |
| 27017 | MongoDB |

---

*DNS broken? Start with `dig`. Port in use? Start with `lsof`.*
