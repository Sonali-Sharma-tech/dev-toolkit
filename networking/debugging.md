# Network Debugging Guide

When you can't connect, follow this guide to find out why.

## Table of Contents
- [Real-World Workflows](#real-world-workflows)
- [Troubleshooting Flowchart](#troubleshooting-flowchart)
- [Is It DNS?](#is-it-dns)
- [Is the Port Open?](#is-the-port-open)
- [Is the Service Running?](#is-the-service-running)
- [Is There a Firewall?](#is-there-a-firewall)
- [Can You Reach the Network?](#can-you-reach-the-network)
- [Common Error Messages](#common-error-messages)
- [Quick Diagnosis Commands](#quick-diagnosis-commands)

---

## Real-World Workflows

### Scenario 1: "My API Can't Connect to the Database"

**Situation:** Your Node.js app throws `ECONNREFUSED` when connecting to PostgreSQL.

```bash
# Step 1: Is the database running?
docker ps | grep postgres
# or
systemctl status postgresql

# Step 2: Is it listening on the right port?
ss -tlnp | grep 5432
# Should show: LISTEN 0 128 0.0.0.0:5432

# Step 3: Can you connect locally?
nc -zv localhost 5432
# or
psql -h localhost -U postgres -c "SELECT 1"

# Step 4: Is your app using the right host?
# Check your connection string:
# - "localhost" only works if app and DB are on same machine
# - Use container name in Docker networks
# - Use actual IP for remote databases

# Step 5: Check DB logs for connection attempts
docker logs postgres-container | tail -20
```

**Common fixes:**
- Database bound to `127.0.0.1` instead of `0.0.0.0`
- Wrong port in connection string
- Docker network not set up correctly

---

### Scenario 2: "Website Works Locally but Not from Other Machines"

**Situation:** `curl localhost:3000` works, but others can't access your dev server.

```bash
# Step 1: What interface is the server bound to?
ss -tlnp | grep 3000
# Bad:  127.0.0.1:3000 (localhost only)
# Good: 0.0.0.0:3000 (all interfaces)

# Step 2: Fix the binding
# Node.js: app.listen(3000, '0.0.0.0')
# Python: flask run --host=0.0.0.0
# React:  HOST=0.0.0.0 npm start

# Step 3: Check firewall
sudo ufw status                    # Linux
sudo iptables -L -n | grep 3000    # Linux
# macOS: System Preferences → Security → Firewall

# Step 4: Find your IP for others to use
ip addr | grep inet                # Linux
ifconfig | grep inet               # macOS
# Share: http://192.168.1.x:3000
```

---

### Scenario 3: "SSH Connection Keeps Timing Out"

**Situation:** `ssh user@server` hangs and eventually times out.

```bash
# Step 1: Is the server reachable at all?
ping server-ip

# Step 2: Is SSH port open?
nc -zv server-ip 22 -w 5
# If timeout → firewall or SSH not running

# Step 3: Try verbose mode for clues
ssh -vvv user@server

# Step 4: Is SSH running on the server? (if you have console access)
sudo systemctl status sshd
ss -tlnp | grep :22

# Step 5: Check if firewall allows SSH
sudo ufw status | grep 22
sudo iptables -L -n | grep 22

# Step 6: Check SSH config for restrictions
cat /etc/ssh/sshd_config | grep -E "^(Port|ListenAddress|AllowUsers)"
```

**Common fixes:**
- SSH running on non-standard port
- Firewall blocking port 22
- Cloud security group not allowing inbound SSH
- VPN required to reach the network

---

### Scenario 4: "Can't Pull Docker Images"

**Situation:** `docker pull nginx` times out or fails.

```bash
# Step 1: Can you reach Docker Hub?
ping registry-1.docker.io
curl -I https://registry-1.docker.io/v2/

# Step 2: DNS working?
nslookup registry-1.docker.io
dig registry-1.docker.io

# Step 3: Proxy issues?
echo $HTTP_PROXY $HTTPS_PROXY

# Step 4: Check Docker daemon config
cat /etc/docker/daemon.json

# Step 5: Test with explicit registry
docker pull docker.io/library/nginx

# Step 6: Check Docker logs
sudo journalctl -u docker -f
```

**Common fixes:**
- Corporate proxy not configured in Docker
- DNS not resolving Docker Hub
- Firewall blocking HTTPS (443)

---

### Scenario 5: "My Container Can't Reach the Internet"

**Situation:** Container starts but can't `curl` external URLs.

```bash
# Step 1: Test from inside container
docker exec -it container-name sh
ping 8.8.8.8        # Can reach internet?
ping google.com     # DNS works?

# Step 2: Check container DNS
docker exec container-name cat /etc/resolv.conf

# Step 3: Check Docker network
docker network inspect bridge

# Step 4: Check host iptables NAT
sudo iptables -t nat -L -n | grep MASQUERADE

# Step 5: Check Docker daemon DNS config
cat /etc/docker/daemon.json | grep dns
```

**Common fixes:**
- Docker DNS not configured: Add `{"dns": ["8.8.8.8"]}` to daemon.json
- iptables FORWARD policy is DROP
- Missing NAT/MASQUERADE rules

---

### Scenario 6: "API Requests Work in Postman but Not in Browser"

**Situation:** CORS errors in browser, but API works fine in Postman/curl.

```bash
# Step 1: This isn't a network issue - it's CORS
# Browsers enforce CORS, curl/Postman don't

# Step 2: Check response headers
curl -I http://api.example.com/endpoint
# Look for: Access-Control-Allow-Origin

# Step 3: Check preflight request
curl -X OPTIONS http://api.example.com/endpoint \
  -H "Origin: http://localhost:3000" \
  -H "Access-Control-Request-Method: POST" -v

# Fix: Configure your API server to send CORS headers
# Access-Control-Allow-Origin: http://localhost:3000
# Access-Control-Allow-Methods: GET, POST, OPTIONS
# Access-Control-Allow-Headers: Content-Type, Authorization
```

---

### Scenario 7: "Deployed App Works But Very Slow"

**Situation:** App responds but takes 10+ seconds.

```bash
# Step 1: Is it network latency?
ping server-ip
# High ms = network issue
# Low ms = app/server issue

# Step 2: Trace the route
traceroute server-ip
mtr server-ip
# Look for high latency hops

# Step 3: Is it DNS lookup delay?
time curl -w "DNS: %{time_namelookup}s\nConnect: %{time_connect}s\nTTFB: %{time_starttransfer}s\nTotal: %{time_total}s\n" -o /dev/null -s http://your-app.com

# Step 4: Check if it's the database
# (On server) Monitor query times
# PostgreSQL: \timing on
# MySQL: SET profiling = 1;

# Step 5: Check server resources
ssh server "top -bn1 | head -20"
ssh server "free -m"
ssh server "df -h"
```

**Common causes:**
- Slow DNS resolution
- Database queries not indexed
- Server out of memory (swapping)
- Cold start (serverless/containers)

---

## Troubleshooting Flowchart

```
Can't connect to service?
           │
           ▼
┌─────────────────────────┐
│ Can you ping the host?  │
│ ping hostname           │
└───────────┬─────────────┘
            │
     ┌──────┴──────┐
     │             │
    YES           NO
     │             │
     ▼             ▼
┌─────────┐  ┌─────────────────┐
│ Port    │  │ Is it DNS?      │
│ check   │  │ ping IP instead │
└────┬────┘  └────────┬────────┘
     │                │
     │         ┌──────┴──────┐
     │        YES           NO
     │         │             │
     │         ▼             ▼
     │    Fix DNS      Network issue
     │    (see below)  (routing/firewall)
     │
     ▼
┌─────────────────────────┐
│ Is port open?           │
│ nc -zv host port        │
└───────────┬─────────────┘
            │
     ┌──────┴──────┐
     │             │
    YES           NO
     │             │
     ▼             ▼
Service issue   ┌─────────────────┐
(auth, config)  │ Is service      │
                │ running locally?│
                │ ss -tlnp        │
                └────────┬────────┘
                         │
                  ┌──────┴──────┐
                  │             │
                 YES           NO
                  │             │
                  ▼             ▼
             Firewall      Start the
             blocking      service!
```

---

## Is It DNS?

### Quick DNS Check
```bash
# Try IP directly - if this works but hostname doesn't, it's DNS
ping 8.8.8.8        # Google DNS
ping 1.1.1.1        # Cloudflare DNS

# vs
ping google.com     # If this fails but above works = DNS issue
```

### DNS Lookup Commands
```bash
# Basic lookup
nslookup google.com
dig google.com

# Specific DNS server
nslookup google.com 8.8.8.8
dig @8.8.8.8 google.com

# Get just the IP
dig +short google.com

# Reverse lookup (IP to hostname)
dig -x 8.8.8.8

# Check DNS resolution time
dig google.com | grep "Query time"
```

### DNS Troubleshooting
```bash
# Check configured DNS servers
cat /etc/resolv.conf                # Linux/Mac
scutil --dns                        # macOS

# Flush DNS cache
sudo dscacheutil -flushcache        # macOS
sudo systemd-resolve --flush-caches # Linux (systemd)
sudo resolvectl flush-caches        # Linux (newer)

# Test with different DNS
nslookup example.com 8.8.8.8        # Google
nslookup example.com 1.1.1.1        # Cloudflare
```

### Common DNS Fixes
```bash
# Temporarily use different DNS
# Add to /etc/resolv.conf (will reset on reboot)
nameserver 8.8.8.8
nameserver 1.1.1.1

# Check /etc/hosts for overrides
cat /etc/hosts
```

---

## Is the Port Open?

### Check Remote Port
```bash
# Netcat (most reliable)
nc -zv hostname 80
nc -zv hostname 443
nc -zv hostname 22

# Timeout after 5 seconds
nc -zv -w 5 hostname 3000

# Check multiple ports
nc -zv hostname 80 443 8080

# Using telnet
telnet hostname 80

# Using curl (for HTTP)
curl -v telnet://hostname:22
```

### Check Local Listening Ports
```bash
# All listening ports (Linux)
ss -tlnp

# All listening ports (macOS)
lsof -iTCP -sTCP:LISTEN -P -n

# Specific port
ss -tlnp | grep :3000
lsof -i :3000

# What's using a port
lsof -i :8080
fuser 8080/tcp

# All connections (not just listening)
ss -tanp
netstat -an
```

### Port States Explained
```
LISTEN      - Service is waiting for connections
ESTABLISHED - Active connection
TIME_WAIT   - Connection closing, port temporarily unavailable
CLOSE_WAIT  - Remote closed, waiting for local close
```

---

## Is the Service Running?

### Check Service Status
```bash
# systemd services
systemctl status nginx
systemctl status docker
systemctl status sshd

# Check if process is running
ps aux | grep nginx
pgrep -a nginx

# Docker containers
docker ps
docker ps -a  # Include stopped
```

### Test Service Locally
```bash
# HTTP service
curl localhost:3000
curl -I localhost:80  # Headers only

# Any TCP service
nc -zv localhost 5432  # PostgreSQL
nc -zv localhost 6379  # Redis

# Check service logs
journalctl -u nginx -f
docker logs container-name -f
tail -f /var/log/nginx/error.log
```

### Start/Restart Service
```bash
# systemd
sudo systemctl start nginx
sudo systemctl restart nginx
sudo systemctl enable nginx  # Start on boot

# Docker
docker start container-name
docker restart container-name
```

---

## Is There a Firewall?

### Check Firewall Status

**macOS:**
```bash
# Check application firewall
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --getglobalstate

# List allowed apps
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --listapps
```

**Linux (iptables):**
```bash
# List all rules
sudo iptables -L -n -v

# List NAT rules
sudo iptables -t nat -L -n -v

# Check if port is allowed
sudo iptables -L -n | grep 80
```

**Linux (ufw):**
```bash
# Check status
sudo ufw status verbose

# List rules
sudo ufw status numbered
```

**Linux (firewalld):**
```bash
# Check status
sudo firewall-cmd --state

# List allowed services/ports
sudo firewall-cmd --list-all
```

### Open a Port

**Linux (ufw):**
```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 22/tcp
```

**Linux (firewalld):**
```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --reload
```

**Linux (iptables):**
```bash
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

---

## Can You Reach the Network?

### Basic Connectivity
```bash
# Ping gateway
ip route | grep default  # Find gateway
ping 192.168.1.1         # Ping it

# Ping external
ping 8.8.8.8
ping 1.1.1.1

# Check route to host
traceroute google.com
traceroute -n google.com  # Skip DNS lookup

# macOS alternative
traceroute google.com
mtr google.com  # Continuous traceroute
```

### Network Interface Info
```bash
# Show interfaces
ip addr                  # Linux
ifconfig                 # macOS/older Linux

# Show routing table
ip route                 # Linux
netstat -rn              # macOS

# Check if interface is up
ip link show eth0
```

### Common Network Issues
```bash
# Interface down
sudo ip link set eth0 up

# No IP address (DHCP issue)
sudo dhclient eth0       # Request new IP

# Wrong gateway
sudo ip route add default via 192.168.1.1
```

---

## Common Error Messages

### "Connection Refused"
```bash
# Service not running or not listening on that port
# Check if service is running
ss -tlnp | grep :PORT

# Check if service is bound to correct interface
# 127.0.0.1 = localhost only
# 0.0.0.0 = all interfaces
```

### "Connection Timed Out"
```bash
# Firewall or routing issue
# Check firewall
sudo iptables -L -n | grep PORT

# Check if host is reachable
ping hostname

# Trace the route
traceroute hostname
```

### "Name or Service Not Known"
```bash
# DNS issue
# Check DNS resolution
nslookup hostname

# Try IP directly
ping IP_ADDRESS

# Check /etc/hosts
cat /etc/hosts
```

### "Permission Denied"
```bash
# SSH/auth issue
# Check SSH key
ssh -v user@host

# Check file permissions
ls -la ~/.ssh/

# Correct SSH key permissions
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
chmod 700 ~/.ssh/
```

### "Address Already in Use"
```bash
# Another process using the port
# Find what's using it
lsof -i :PORT
fuser PORT/tcp

# Kill the process
kill $(lsof -t -i :PORT)
fuser -k PORT/tcp
```

---

## Quick Diagnosis Commands

### One-Liner Checks
```bash
# Full connectivity test
ping -c1 host && nc -zv host port && echo "Success!"

# DNS + Port check
dig +short host && nc -zv host 443

# What's my IP
curl -s ifconfig.me
curl -s icanhazip.com
```

### Network Summary
```bash
# Quick network status
echo "=== Interfaces ===" && ip addr | grep inet
echo "=== Gateway ===" && ip route | grep default
echo "=== DNS ===" && cat /etc/resolv.conf | grep nameserver
echo "=== Listening Ports ===" && ss -tlnp
```

### Save for Later
```bash
# Dump network state to file
{
    echo "=== Date ==="
    date
    echo "=== IP ==="
    ip addr
    echo "=== Routes ==="
    ip route
    echo "=== DNS ==="
    cat /etc/resolv.conf
    echo "=== Listening ==="
    ss -tlnp
    echo "=== Connections ==="
    ss -tanp
} > network-debug-$(date +%Y%m%d).txt
```

---

## Quick Reference

| Problem | Command |
|---------|---------|
| Check DNS | `dig hostname` or `nslookup hostname` |
| Check port open | `nc -zv hostname port` |
| Check listening ports | `ss -tlnp` or `lsof -iTCP -sTCP:LISTEN` |
| Check firewall | `sudo iptables -L -n` or `sudo ufw status` |
| Trace route | `traceroute hostname` |
| Check service | `systemctl status service` |
| Find process on port | `lsof -i :port` |
| Test HTTP | `curl -v http://hostname` |
| Check network interface | `ip addr` or `ifconfig` |
| Flush DNS | `sudo dscacheutil -flushcache` (macOS) |

