# SSH Error Messages Decoded

When SSH throws an error, find it here.

## Table of Contents
- [Permission denied (publickey)](#permission-denied-publickey)
- [Connection refused](#connection-refused)
- [Connection timed out](#connection-timed-out)
- [Host key verification failed](#host-key-verification-failed)
- [WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED](#warning-remote-host-identification-has-changed)
- [Too many authentication failures](#too-many-authentication-failures)
- [No such file or directory (key)](#no-such-file-or-directory-key)
- [Bad permissions on key file](#bad-permissions-on-key-file)
- [Could not resolve hostname](#could-not-resolve-hostname)
- [Connection reset by peer](#connection-reset-by-peer)
- [Agent admitted failure to sign](#agent-admitted-failure-to-sign)

---

## Permission denied (publickey)

```
Permission denied (publickey).
```

### What it means
The server rejected your SSH key authentication.

### Quick fix
```bash
# Check if SSH agent has your key
ssh-add -l

# If empty, add your key
ssh-add ~/.ssh/id_ed25519

# Test connection
ssh -T git@github.com
```

### If you don't have a key
```bash
# Generate new SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"

# Start SSH agent
eval "$(ssh-agent -s)"

# Add key to agent
ssh-add ~/.ssh/id_ed25519

# Copy public key and add to GitHub/server
cat ~/.ssh/id_ed25519.pub
```

### Debug with verbose mode
```bash
ssh -vvv user@server
# Look for "Offering public key" and "Server accepts key" lines
```

### Common causes
- Key not added to server/GitHub
- Wrong key being used
- Key not added to SSH agent
- Key has passphrase but agent doesn't have it

---

## Connection refused

```
ssh: connect to host 192.168.1.100 port 22: Connection refused
```

### What it means
The server is reachable but refusing connections on that port.

### Quick fix
```bash
# Is SSH server running? (on the server)
sudo systemctl status sshd
ss -tlnp | grep :22

# Start SSH server if needed
sudo systemctl start sshd
```

### If SSH is on a different port
```bash
# Try common alternative ports
ssh -p 2222 user@server
ssh -p 443 user@server

# Check server's SSH config
cat /etc/ssh/sshd_config | grep Port
```

### Common causes
- SSH server not running
- SSH on different port
- Firewall blocking port 22
- Server not listening on your IP

---

## Connection timed out

```
ssh: connect to host server.example.com port 22: Connection timed out
```

### What it means
No response from the server at all.

### Quick fix
```bash
# Is server reachable at all?
ping server.example.com

# Is port 22 open?
nc -zv server.example.com 22 -w 5

# Try traceroute to find where it's blocked
traceroute server.example.com
```

### Common causes
- Server is down
- Wrong IP/hostname
- Firewall blocking (your side or server side)
- Network routing issue
- Need VPN to reach the network

### If behind a corporate firewall
```bash
# SSH over HTTPS port (if server supports it)
ssh -p 443 user@server

# Or use SSH over HTTP proxy
ssh -o "ProxyCommand=nc -X connect -x proxy:8080 %h %p" user@server
```

---

## Host key verification failed

```
Host key verification failed.
```

### What it means
SSH can't verify the server's identity, usually because known_hosts entry is missing or incorrect.

### Quick fix
```bash
# Remove old key for the host
ssh-keygen -R hostname

# Connect again and accept the new key
ssh user@hostname
# Type "yes" when prompted

# Or add key manually
ssh-keyscan hostname >> ~/.ssh/known_hosts
```

---

## WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
```

### What it means
The server's key changed since you last connected. This could be:
- Server was reinstalled
- Server key was rotated
- **Or someone is intercepting your connection (MITM attack)**

### Quick fix (if you trust the change)
```bash
# Remove the old key
ssh-keygen -R hostname

# Or with IP address
ssh-keygen -R 192.168.1.100

# Connect again
ssh user@hostname
# Type "yes" to accept new key
```

### If you're not sure
```bash
# Contact server admin to verify the new key fingerprint
# Compare with:
ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub  # on server
```

### Automatic reset (for ephemeral servers)
```bash
# For cloud VMs that change frequently
# Add to ~/.ssh/config:
Host dev-server
    StrictHostKeyChecking no
    UserKnownHostsFile /dev/null
```

---

## Too many authentication failures

```
Received disconnect from 192.168.1.100 port 22:
Too many authentication failures
```

### What it means
SSH tried too many keys before finding the right one.

### Quick fix
```bash
# Specify the exact key to use
ssh -i ~/.ssh/my_specific_key user@server

# Or clear the SSH agent
ssh-add -D

# Add only the key you need
ssh-add ~/.ssh/correct_key
```

### Set in SSH config
```bash
# ~/.ssh/config
Host myserver
    HostName server.example.com
    User myuser
    IdentityFile ~/.ssh/specific_key
    IdentitiesOnly yes
```

---

## No such file or directory (key)

```
Warning: Identity file ~/.ssh/my_key not found
```

### What it means
The key file you specified doesn't exist.

### Quick fix
```bash
# Check what keys you have
ls -la ~/.ssh/

# Generate a new key if needed
ssh-keygen -t ed25519 -C "your.email@example.com"

# Check you're using the right filename
# Default names:
# ~/.ssh/id_ed25519
# ~/.ssh/id_rsa
```

---

## Bad permissions on key file

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0644 for '/home/user/.ssh/id_ed25519' are too open.
```

### What it means
Your private key file is readable by others. SSH refuses to use it for security.

### Quick fix
```bash
# Fix private key permissions
chmod 600 ~/.ssh/id_ed25519
chmod 600 ~/.ssh/id_rsa

# Fix public key permissions
chmod 644 ~/.ssh/id_ed25519.pub

# Fix .ssh directory permissions
chmod 700 ~/.ssh
```

### Full permissions reset
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_*
chmod 644 ~/.ssh/*.pub
chmod 644 ~/.ssh/known_hosts
chmod 600 ~/.ssh/config
```

---

## Could not resolve hostname

```
ssh: Could not resolve hostname server.example.com: nodename nor servname provided
```

### What it means
DNS can't resolve the hostname.

### Quick fix
```bash
# Check if hostname resolves
nslookup server.example.com
dig server.example.com

# Try using IP directly
ssh user@192.168.1.100

# Check /etc/hosts
cat /etc/hosts

# Try different DNS
nslookup server.example.com 8.8.8.8
```

### Common causes
- Typo in hostname
- DNS server is down
- Host only reachable via VPN
- Host is on internal network

---

## Connection reset by peer

```
Connection reset by peer
```

### What it means
The server forcefully closed the connection.

### Quick fix
```bash
# Check server logs (on server)
sudo tail -f /var/log/auth.log
# or
sudo journalctl -u sshd -f

# Enable keep-alive (in ~/.ssh/config)
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

### Common causes
- Server crashed
- Firewall killed idle connection
- Server's SSH config denied you
- Network issue

### Check server SSH config
```bash
# /etc/ssh/sshd_config
AllowUsers myuser              # Only these users allowed
DenyUsers baduser              # Blocked users
MaxAuthTries 3                 # Auth attempt limit
ClientAliveInterval 300        # Timeout settings
```

---

## Agent admitted failure to sign

```
Agent admitted failure to sign using the key.
```

### What it means
SSH agent has the key but couldn't use it.

### Quick fix
```bash
# Restart SSH agent
eval "$(ssh-agent -s)"

# Re-add the key
ssh-add ~/.ssh/id_ed25519

# If key has passphrase, you'll need to enter it
```

### On macOS
```bash
# Add to Keychain
ssh-add --apple-use-keychain ~/.ssh/id_ed25519

# Load from Keychain automatically (~/.ssh/config)
Host *
    AddKeysToAgent yes
    UseKeychain yes
    IdentityFile ~/.ssh/id_ed25519
```

---

## Quick Reference

| Error | Likely Cause | Quick Fix |
|-------|--------------|-----------|
| Permission denied (publickey) | Key not authorized | `ssh-add` your key |
| Connection refused | SSH not running | Start SSH server |
| Connection timed out | Firewall/routing | Check firewall, VPN |
| Host key verification failed | New/changed server | `ssh-keygen -R host` |
| REMOTE HOST CHANGED | Server reinstalled | Remove old key (verify first!) |
| Too many auth failures | Wrong keys tried | Specify key with `-i` |
| Key not found | Wrong path | Check `~/.ssh/` |
| Bad permissions | Key too open | `chmod 600` on private key |
| Could not resolve hostname | DNS issue | Check DNS, try IP |
| Connection reset | Server closed | Check server logs |

---

## SSH Debug Mode

When nothing else works, use verbose mode:

```bash
# Level 1
ssh -v user@server

# Level 2 (more detail)
ssh -vv user@server

# Level 3 (maximum detail)
ssh -vvv user@server
```

### What to look for
```
debug1: Offering public key: /home/user/.ssh/id_ed25519
debug1: Server accepts key: /home/user/.ssh/id_ed25519
```

If you don't see "Server accepts key", the key isn't authorized on the server.

