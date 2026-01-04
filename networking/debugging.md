# Network Debugging Guide

When you can't connect, follow this guide to find out why.

## Table of Contents
- [Troubleshooting Flowchart](#troubleshooting-flowchart)
- [Is It DNS?](#is-it-dns)
- [Is the Port Open?](#is-the-port-open)
- [Is the Service Running?](#is-the-service-running)
- [Is There a Firewall?](#is-there-a-firewall)
- [Can You Reach the Network?](#can-you-reach-the-network)
- [Common Scenarios](#common-scenarios)
- [Quick Diagnosis Commands](#quick-diagnosis-commands)

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

## Common Scenarios

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

