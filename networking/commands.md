# Networking Commands Reference

Essential networking commands for developers. From checking connectivity to debugging API calls.

## Table of Contents
- [Installation](#installation)
- [Connectivity & DNS](#connectivity--dns)
- [Network Discovery](#discover-devices-on-local-network-lanwi-fi)
- [HTTP Requests (curl)](#http-requests-curl)
- [API Testing](#api-testing)
- [SSH & Remote Access](#ssh--remote-access)
- [Port & Process Management](#port--process-management)
- [File Transfer](#file-transfer)
- [Network Debugging](#network-debugging)
- [Firewall & Security](#firewall--security)

---

## Installation

Most tools come pre-installed on macOS/Linux. Here's how to install missing ones:

### macOS (using Homebrew)
```bash
# Install Homebrew first (if not installed)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install networking tools
brew install curl              # HTTP client (usually pre-installed)
brew install wget              # File downloader
brew install httpie            # Modern HTTP client (easier than curl)
brew install nmap              # Network scanner
brew install netcat            # TCP/UDP connections
brew install telnet            # Telnet client
brew install openssh           # SSH client (usually pre-installed)
brew install rsync             # File sync (usually pre-installed)
brew install jq                # JSON processor
```

### Ubuntu/Debian
```bash
sudo apt update

sudo apt install curl          # HTTP client
sudo apt install wget          # File downloader
sudo apt install httpie        # Modern HTTP client
sudo apt install nmap          # Network scanner
sudo apt install netcat        # TCP/UDP connections
sudo apt install telnet        # Telnet client
sudo apt install openssh-client # SSH client
sudo apt install rsync         # File sync
sudo apt install jq            # JSON processor
sudo apt install dnsutils      # dig, nslookup
sudo apt install net-tools     # ifconfig, netstat (legacy)
sudo apt install iproute2      # ip command (modern)
```

### CentOS/RHEL/Fedora
```bash
sudo dnf install curl wget httpie nmap nc telnet openssh-clients rsync jq bind-utils net-tools iproute
```

### Verify Installation
```bash
curl --version
wget --version
ssh -V
nmap --version
```

---

## Connectivity & DNS

### Check if host is reachable
```bash
ping google.com                 # Continuous ping (Ctrl+C to stop)
ping -c 4 google.com            # Send 4 packets only
```

### DNS Lookup
```bash
# Get IP address of domain
nslookup google.com
dig google.com +short           # Cleaner output
host google.com                 # Simple format

# Get detailed DNS records
dig google.com                  # Full DNS response
dig google.com ANY              # All record types
dig google.com MX               # Mail server records
dig google.com TXT              # TXT records (SPF, DKIM)
dig google.com NS               # Name server records
```

### Reverse DNS (IP to hostname)
```bash
dig -x 8.8.8.8 +short
nslookup 8.8.8.8
host 8.8.8.8
```

### Check DNS propagation
```bash
# Query specific DNS server
dig @8.8.8.8 example.com        # Google DNS
dig @1.1.1.1 example.com        # Cloudflare DNS
dig @208.67.222.222 example.com # OpenDNS
```

### Trace route to host
```bash
traceroute google.com           # Linux/macOS
tracepath google.com            # Linux alternative
mtr google.com                  # Real-time traceroute (install: brew install mtr)
```

### Discover devices on local network (LAN/Wi-Fi)
```bash
# Show ARP cache - IPs/MACs your machine has recently talked to (quick but incomplete)
arp -a

# Ping scan entire subnet - /24 = all 256 IPs (192.168.1.0 to 192.168.1.255)
# -sn = no port scan, just check if hosts are alive
nmap -sn 192.168.1.0/24
nmap -sn 192.168.0.0/24                 # Try this if above doesn't match your network

# Explicit range syntax - scan .1 through .254
nmap -sn 192.168.1.1-254

# Count live hosts - grep matches "Nmap scan report for..." per device
nmap -sn 192.168.1.0/24 | grep "Nmap scan" | wc -l

# With sudo: uses raw sockets to show MAC addresses + vendor (Apple, Samsung, etc.)
sudo nmap -sn 192.168.1.0/24

# arp-scan: faster than nmap (uses ARP protocol directly, requires root)
# Install: brew install arp-scan (macOS) or sudo apt install arp-scan (Linux)
sudo arp-scan --localnet               # Scan all local interfaces
sudo arp-scan -I en0 --localnet        # en0 = Wi-Fi on macOS
sudo arp-scan -I eth0 --localnet       # eth0/wlan0 = common Linux interfaces

# Find your subnet first (so you know what range to scan)
# macOS: get your IP + show default gateway (router IP)
ipconfig getifaddr en0 && netstat -rn | grep default
# Linux: show default route with gateway and interface
ip route | grep default
# Linux: get just your local IP (awk extracts first IP if multiple)
hostname -I | awk '{print $1}'
```

**Quick one-liner to count devices on network:**
```bash
# Linux: extract your IP from route table, append /24, scan and count
# -oP with \K = regex lookbehind to capture IP after "src "
nmap -sn $(ip route | grep -oP 'src \K[\d.]+' | head -1)/24 2>/dev/null | grep -c "Nmap scan"

# macOS: get IP, use sed to replace last octet with .0, scan subnet
# e.g., 192.168.1.45 becomes 192.168.1.0/24
nmap -sn $(ipconfig getifaddr en0 | sed 's/\.[0-9]*$/.0/')/24 | grep -c "Nmap scan"
```

### Get your IP address
```bash
# Public IP
curl -s ifconfig.me
curl -s icanhazip.com
curl -s ipinfo.io/ip
curl -s ipinfo.io               # Full details (location, ISP, etc.)

# Local IP
ipconfig getifaddr en0          # macOS (Wi-Fi)
ipconfig getifaddr en1          # macOS (Ethernet)
hostname -I | awk '{print $1}'  # Linux
ip addr show | grep "inet " | grep -v 127.0.0.1 | awk '{print $2}'  # Linux
```

### Check network interfaces
```bash
ifconfig                        # macOS/older Linux
ip addr                         # Modern Linux
ip link show                    # Interface status
```

---

## HTTP Requests (curl)

### Basic GET request
```bash
curl https://api.example.com
curl -s https://api.example.com           # Silent (no progress bar)
curl -v https://api.example.com           # Verbose (debug info)
```

### See response headers
```bash
curl -I https://example.com               # Headers only (HEAD request)
curl -i https://example.com               # Headers + body
```

### GET with query parameters
```bash
curl "https://api.example.com/search?q=term&limit=10"
```

### POST request with JSON
```bash
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d '{"name": "John", "email": "john@example.com"}'
```

### POST with form data
```bash
curl -X POST https://api.example.com/login \
  -d "username=john" \
  -d "password=secret"

# Or in one line
curl -X POST https://api.example.com/login -d "username=john&password=secret"
```

### PUT request
```bash
curl -X PUT https://api.example.com/users/123 \
  -H "Content-Type: application/json" \
  -d '{"name": "John Updated"}'
```

### DELETE request
```bash
curl -X DELETE https://api.example.com/users/123
```

### PATCH request
```bash
curl -X PATCH https://api.example.com/users/123 \
  -H "Content-Type: application/json" \
  -d '{"status": "active"}'
```

### Add headers
```bash
curl https://api.example.com \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Accept: application/json" \
  -H "X-Custom-Header: value"
```

### Save response to file
```bash
curl -o output.json https://api.example.com/data
curl -O https://example.com/file.zip       # Keep original filename
```

### Download file with progress
```bash
curl -# -O https://example.com/largefile.zip
```

### Follow redirects
```bash
curl -L https://short.url/abc              # Follow 301/302 redirects
curl -L -o file.zip https://example.com/download  # Download after redirect
```

### Get only HTTP status code
```bash
curl -s -o /dev/null -w "%{http_code}" https://example.com
```

### Measure response time
```bash
curl -s -o /dev/null -w "Connect: %{time_connect}s\nTTFB: %{time_starttransfer}s\nTotal: %{time_total}s\n" https://example.com
```

### Upload file
```bash
curl -X POST https://api.example.com/upload \
  -F "file=@/path/to/file.pdf" \
  -F "description=My document"
```

### Basic authentication
```bash
curl -u username:password https://api.example.com
curl https://username:password@api.example.com  # URL format
```

### Use cookies
```bash
curl -c cookies.txt https://example.com/login -d "user=john"  # Save cookies
curl -b cookies.txt https://example.com/dashboard             # Send cookies
```

### Ignore SSL certificate errors
```bash
curl -k https://self-signed.example.com    # Use with caution!
```

### Set timeout
```bash
curl --connect-timeout 5 --max-time 10 https://slow-api.com
```

---

## API Testing

### Pretty print JSON response
```bash
curl -s https://api.example.com | jq '.'
curl -s https://api.example.com | python3 -m json.tool
```

### Extract specific field from JSON
```bash
curl -s https://api.example.com/user | jq '.name'
curl -s https://api.example.com/users | jq '.[0].email'   # First user's email
curl -s https://api.example.com/users | jq '.[].name'     # All names
```

### HTTPie (easier than curl)
```bash
# Install: brew install httpie (macOS) or pip install httpie

# GET request
http https://api.example.com

# POST with JSON (automatic Content-Type)
http POST https://api.example.com/users name=John email=john@example.com

# With headers
http https://api.example.com Authorization:"Bearer TOKEN"

# Form data
http --form POST https://api.example.com/login username=john password=secret

# Download file
http --download https://example.com/file.zip
```

### Test REST API endpoints (complete flow)
```bash
# Create (POST)
curl -X POST http://localhost:3000/api/users \
  -H "Content-Type: application/json" \
  -d '{"name": "John", "email": "john@example.com"}' | jq

# Read (GET)
curl -s http://localhost:3000/api/users | jq
curl -s http://localhost:3000/api/users/1 | jq

# Update (PUT)
curl -X PUT http://localhost:3000/api/users/1 \
  -H "Content-Type: application/json" \
  -d '{"name": "John Doe"}' | jq

# Delete (DELETE)
curl -X DELETE http://localhost:3000/api/users/1

# Verify deletion
curl -s http://localhost:3000/api/users/1  # Should return 404
```

### Test webhook locally
```bash
# Start a simple server to receive webhooks
nc -l 8080                                  # Listen on port 8080

# In another terminal, send test webhook
curl -X POST http://localhost:8080/webhook \
  -H "Content-Type: application/json" \
  -d '{"event": "test", "data": {"id": 123}}'
```

---

## SSH & Remote Access

### Basic SSH connection
```bash
ssh user@hostname
ssh user@192.168.1.100
ssh -p 2222 user@hostname       # Custom port
```

### SSH with key
```bash
ssh -i ~/.ssh/my_key.pem user@hostname
```

### Generate SSH key pair
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"    # Recommended
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" # RSA alternative

# Keys are saved to:
# ~/.ssh/id_ed25519 (private)
# ~/.ssh/id_ed25519.pub (public)
```

### Copy SSH key to server
```bash
ssh-copy-id user@hostname
ssh-copy-id -i ~/.ssh/my_key.pub user@hostname  # Specific key

# Manual method
cat ~/.ssh/id_ed25519.pub | ssh user@hostname "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

### SSH config file (~/.ssh/config)
```bash
# Create/edit config
nano ~/.ssh/config
```

```
# Example config
Host myserver
    HostName 192.168.1.100
    User john
    Port 22
    IdentityFile ~/.ssh/my_key

Host production
    HostName prod.example.com
    User deploy
    IdentityFile ~/.ssh/prod_key

Host *
    AddKeysToAgent yes
    IdentitiesOnly yes
```

```bash
# Now connect with just:
ssh myserver
ssh production
```

### SSH tunneling (port forwarding)

#### Local port forwarding
```bash
# Access remote localhost:3000 via local localhost:8080
ssh -L 8080:localhost:3000 user@remote-server

# Access remote database (not exposed to internet)
ssh -L 5432:localhost:5432 user@remote-server
# Now connect to localhost:5432 to reach remote Postgres
```

#### Remote port forwarding
```bash
# Expose local port 3000 on remote server's port 9090
ssh -R 9090:localhost:3000 user@remote-server
# Others can access remote-server:9090 to reach your localhost:3000
```

#### Dynamic port forwarding (SOCKS proxy)
```bash
ssh -D 9999 user@remote-server
# Configure browser to use SOCKS5 proxy: localhost:9999
```

### Keep SSH session alive
```bash
# Add to ~/.ssh/config
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

### Run command on remote server
```bash
ssh user@hostname "ls -la /var/log"
ssh user@hostname "df -h && free -m"
ssh user@hostname 'bash -s' < local_script.sh   # Run local script remotely
```

### SSH agent (avoid typing passphrase)
```bash
eval "$(ssh-agent -s)"          # Start agent
ssh-add ~/.ssh/id_ed25519       # Add key
ssh-add -l                      # List added keys
```

---

## Port & Process Management

### Check if port is open
```bash
nc -zv hostname 80              # Check port 80
nc -zv hostname 22              # Check SSH port
nc -zv hostname 1-1000          # Scan port range (slow)
```

### Check what's using a port
```bash
# macOS
lsof -i :8080
lsof -i :8080 | grep LISTEN

# Linux
ss -tulpn | grep 8080
netstat -tulpn | grep 8080      # Older systems
fuser 8080/tcp                  # Just PID
```

### List all listening ports
```bash
# macOS
lsof -i -P -n | grep LISTEN
netstat -an | grep LISTEN

# Linux
ss -tulpn
netstat -tulpn
```

### Kill process on port
```bash
# macOS/Linux
kill -9 $(lsof -t -i:8080)

# Or find PID first
lsof -i :8080                   # Note the PID
kill -9 <PID>
```

### Check open connections
```bash
netstat -an | grep ESTABLISHED
ss -t state established         # Linux
lsof -i -P -n | grep ESTABLISHED
```

### Scan ports (nmap)
```bash
nmap hostname                   # Common ports
nmap -p 80,443,8080 hostname    # Specific ports
nmap -p 1-65535 hostname        # All ports (slow)
nmap -sV hostname               # Detect service versions
nmap -A hostname                # Aggressive scan (OS, versions, scripts)
```

---

## File Transfer

### SCP (Secure Copy)
```bash
# Local to remote
scp file.txt user@hostname:/path/to/destination/
scp -r folder/ user@hostname:/path/to/destination/  # Recursive

# Remote to local
scp user@hostname:/path/to/file.txt ./local/
scp -r user@hostname:/path/to/folder/ ./local/

# Between two remote hosts
scp user1@host1:/path/file.txt user2@host2:/path/

# With custom port
scp -P 2222 file.txt user@hostname:/path/
```

### Rsync (better for large/multiple files)
```bash
# Basic sync (local to remote)
rsync -avz ./folder/ user@hostname:/path/to/destination/

# Remote to local
rsync -avz user@hostname:/path/to/folder/ ./local/

# With progress
rsync -avz --progress ./folder/ user@hostname:/path/

# Delete files on destination that don't exist in source
rsync -avz --delete ./folder/ user@hostname:/path/

# Dry run (see what would happen)
rsync -avz --dry-run ./folder/ user@hostname:/path/

# Exclude files
rsync -avz --exclude='node_modules' --exclude='.git' ./project/ user@hostname:/path/

# With custom SSH port
rsync -avz -e "ssh -p 2222" ./folder/ user@hostname:/path/
```

**Rsync flags explained:**
- `-a` = archive mode (preserves permissions, timestamps, etc.)
- `-v` = verbose
- `-z` = compress during transfer
- `-P` = show progress + keep partially transferred files

### wget (download files)
```bash
wget https://example.com/file.zip
wget -O custom_name.zip https://example.com/file.zip  # Custom filename
wget -c https://example.com/large.zip                  # Resume download
wget -r -np https://example.com/folder/               # Recursive download
wget --limit-rate=1m https://example.com/file.zip     # Limit speed
wget -q https://example.com/file.zip                  # Quiet mode
```

### SFTP (interactive file transfer)
```bash
sftp user@hostname

# SFTP commands:
# ls            - list remote files
# lls           - list local files
# cd path       - change remote directory
# lcd path      - change local directory
# get file      - download file
# put file      - upload file
# mget *.txt    - download multiple files
# mput *.txt    - upload multiple files
# exit          - quit
```

---

## Network Debugging

### Check HTTP response details
```bash
curl -v https://example.com 2>&1 | head -50
```

### Debug DNS issues
```bash
# Check DNS resolution
dig example.com +trace            # Full DNS trace
dig example.com @8.8.8.8          # Use specific DNS
cat /etc/resolv.conf              # Current DNS config

# Flush DNS cache
# macOS
sudo dscacheutil -flushcache && sudo killall -HUP mDNSResponder

# Linux (systemd)
sudo systemd-resolve --flush-caches
```

### Check SSL/TLS certificate
```bash
# View certificate details
openssl s_client -connect example.com:443 -servername example.com </dev/null 2>/dev/null | openssl x509 -text

# Check expiration date
echo | openssl s_client -connect example.com:443 2>/dev/null | openssl x509 -noout -dates

# Quick check
curl -vI https://example.com 2>&1 | grep -A 6 "Server certificate"
```

### Test TCP connection
```bash
# Check if port is open
nc -zv hostname 443
telnet hostname 443               # Older method

# Send raw HTTP request
echo -e "GET / HTTP/1.1\r\nHost: example.com\r\n\r\n" | nc example.com 80
```

### Monitor network traffic
```bash
# Capture packets (requires root)
sudo tcpdump -i any port 80       # HTTP traffic
sudo tcpdump -i any host 1.2.3.4  # Traffic to/from IP
sudo tcpdump -i any -w capture.pcap  # Save to file

# Watch connections in real-time
watch -n 1 'netstat -an | grep ESTABLISHED | wc -l'
```

### Test bandwidth
```bash
# Download speed test
curl -o /dev/null -w "Speed: %{speed_download} bytes/sec\n" https://speed.cloudflare.com/__down?bytes=100000000

# Using speedtest-cli
# Install: pip install speedtest-cli
speedtest-cli
```

### Check network latency
```bash
ping -c 10 google.com | tail -1   # Average latency
mtr --report google.com           # Detailed path analysis
```

---

## Firewall & Security

### macOS Firewall
```bash
# Check status
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --getglobalstate

# Enable/disable
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setglobalstate on
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setglobalstate off
```

### Linux (ufw - Ubuntu)
```bash
sudo ufw status                   # Check status
sudo ufw enable                   # Enable firewall
sudo ufw disable                  # Disable firewall

# Allow/deny ports
sudo ufw allow 80                 # Allow HTTP
sudo ufw allow 443                # Allow HTTPS
sudo ufw allow 22                 # Allow SSH
sudo ufw allow from 192.168.1.0/24  # Allow subnet
sudo ufw deny 3306                # Block MySQL

# Delete rules
sudo ufw delete allow 80
```

### Linux (iptables)
```bash
# List rules
sudo iptables -L -n -v

# Allow port
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# Block IP
sudo iptables -A INPUT -s 1.2.3.4 -j DROP

# Save rules (Ubuntu)
sudo iptables-save > /etc/iptables.rules
```

### Check for open vulnerabilities
```bash
# Scan for common vulnerabilities
nmap --script vuln hostname

# Check SSL vulnerabilities
nmap --script ssl-enum-ciphers -p 443 hostname
```

---

## Quick Reference

### Common Ports
| Port | Service |
|------|---------|
| 20, 21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 3000 | Dev servers (Node, Rails) |
| 3306 | MySQL |
| 5432 | PostgreSQL |
| 6379 | Redis |
| 8080 | HTTP Alternate |
| 27017 | MongoDB |

### HTTP Status Codes
| Code | Meaning |
|------|---------|
| 200 | OK |
| 201 | Created |
| 204 | No Content |
| 301 | Moved Permanently |
| 302 | Found (Redirect) |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 500 | Internal Server Error |
| 502 | Bad Gateway |
| 503 | Service Unavailable |

---

*Pro tip: Combine curl with jq for powerful API debugging: `curl -s api.example.com | jq '.data[] | {id, name}'`*
