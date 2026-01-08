# SSH Mastery

> Keys, config, tunnels, and jump hosts.

## Table of Contents
- [Key Setup](#key-setup)
- [SSH Config File](#ssh-config-file)
- [Connecting](#connecting)
- [File Transfer](#file-transfer)
- [Tunnels and Port Forwarding](#tunnels-and-port-forwarding)
- [Jump Hosts](#jump-hosts)
- [Agent Forwarding](#agent-forwarding)
- [Troubleshooting](#troubleshooting)

---

## Key Setup

### Generate a New Key

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Ed25519 is modern and secure. RSA also works:

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

### Key Location

```
~/.ssh/id_ed25519       # Private key (NEVER share)
~/.ssh/id_ed25519.pub   # Public key (share freely)
```

### Copy Public Key to Server

```bash
ssh-copy-id user@server
```

Or manually:

```bash
cat ~/.ssh/id_ed25519.pub | ssh user@server "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

### Fix Key Permissions

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
chmod 600 ~/.ssh/config
```

---

## SSH Config File

Create `~/.ssh/config` to save connection settings.

### Basic Config

```ssh
# ~/.ssh/config

Host myserver
    HostName 192.168.1.100
    User john
    Port 22

Host prod
    HostName production.example.com
    User deploy
    IdentityFile ~/.ssh/deploy_key
```

Now connect with just:

```bash
ssh myserver
ssh prod
```

### Wildcard Hosts

```ssh
# Apply to all hosts
Host *
    AddKeysToAgent yes
    IdentitiesOnly yes
    ServerAliveInterval 60
    ServerAliveCountMax 3

# Apply to all company servers
Host *.company.com
    User myusername
    IdentityFile ~/.ssh/company_key
```

### Multiple GitHub Accounts

```ssh
# Personal GitHub
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_personal

# Work GitHub
Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_work
```

Clone with work account:

```bash
git clone git@github-work:company/repo.git
```

---

## Connecting

### Basic Connection

```bash
ssh user@hostname
```

### Specify Port

```bash
ssh -p 2222 user@hostname
```

### Specify Key

```bash
ssh -i ~/.ssh/specific_key user@hostname
```

### Run Command and Exit

```bash
ssh user@server "ls -la /var/log"
```

### Run Multiple Commands

```bash
ssh user@server "cd /app && git pull && pm2 restart all"
```

### Interactive Session with Command

```bash
ssh -t user@server "cd /var/log && bash"
```

`-t` forces pseudo-terminal allocation.

---

## File Transfer

### SCP (Secure Copy)

```bash
# Local to remote
scp file.txt user@server:/path/to/destination/

# Remote to local
scp user@server:/path/to/file.txt ./

# Directory (recursive)
scp -r folder/ user@server:/path/to/destination/

# With custom port
scp -P 2222 file.txt user@server:/path/
```

### rsync (Better for Large Transfers)

```bash
# Sync directory
rsync -avz ./local-folder/ user@server:/remote-folder/

# With progress
rsync -avz --progress ./folder/ user@server:/folder/

# Delete files not in source
rsync -avz --delete ./folder/ user@server:/folder/

# Dry run (see what would happen)
rsync -avzn ./folder/ user@server:/folder/
```

### SFTP (Interactive)

```bash
sftp user@server

# Once connected:
ls              # List remote files
lls             # List local files
get file.txt    # Download
put file.txt    # Upload
cd /path        # Change remote directory
lcd /path       # Change local directory
```

---

## Tunnels and Port Forwarding

### Local Port Forwarding

Access a remote service through a local port.

```bash
ssh -L 8080:localhost:80 user@server
```

Now `localhost:8080` → server's `localhost:80`

**Use case**: Access a database only available on server:

```bash
ssh -L 5432:localhost:5432 user@server
# Connect to postgres://localhost:5432
```

### Remote Port Forwarding

Expose a local service to the remote server.

```bash
ssh -R 8080:localhost:3000 user@server
```

Now server's `localhost:8080` → your `localhost:3000`

**Use case**: Share local dev server with teammates.

### Dynamic Port Forwarding (SOCKS Proxy)

```bash
ssh -D 9999 user@server
```

Creates a SOCKS5 proxy on `localhost:9999`. Configure browser to use it.

### Keep Tunnel Open in Background

```bash
ssh -fN -L 8080:localhost:80 user@server
```

`-f` = background, `-N` = no command

### Find and Kill Background Tunnel

```bash
ps aux | grep "ssh -fN"
kill <pid>
```

---

## Jump Hosts

### Connect Through a Bastion

```bash
ssh -J bastion@jump.example.com user@internal-server
```

### In Config File

```ssh
Host internal-server
    HostName 10.0.0.50
    User admin
    ProxyJump bastion

Host bastion
    HostName jump.example.com
    User bastion-user
```

Now just:

```bash
ssh internal-server
```

### Multiple Jumps

```bash
ssh -J user1@jump1,user2@jump2 user@final-server
```

---

## Agent Forwarding

Use your local SSH keys on remote servers.

### Enable for a Session

```bash
ssh -A user@server
```

### In Config

```ssh
Host server
    HostName server.example.com
    ForwardAgent yes
```

### Security Warning

Only use with trusted servers. A compromised server could use your keys.

### Check Agent is Running

```bash
# Start agent
eval "$(ssh-agent -s)"

# Add key
ssh-add ~/.ssh/id_ed25519

# List loaded keys
ssh-add -l
```

---

## Troubleshooting

### Debug Connection

```bash
ssh -v user@server    # Verbose
ssh -vv user@server   # More verbose
ssh -vvv user@server  # Maximum verbosity
```

### "Permission denied (publickey)"

```bash
# Check if key is loaded
ssh-add -l

# Add key to agent
ssh-add ~/.ssh/id_ed25519

# Test connection
ssh -T git@github.com
```

### "Connection refused"

1. Is SSH running? `systemctl status sshd`
2. Is port open? `nc -zv server 22`
3. Is firewall blocking? Check iptables/ufw

### "Host key verification failed"

```bash
# Remove old key
ssh-keygen -R hostname

# Or accept new key
ssh -o StrictHostKeyChecking=no user@server
```

### Slow Connection

Add to config:

```ssh
Host *
    AddressFamily inet  # Use IPv4 only
```

### "Too many authentication failures"

```bash
ssh -o IdentitiesOnly=yes -i ~/.ssh/specific_key user@server
```

Or in config:

```ssh
Host server
    IdentitiesOnly yes
    IdentityFile ~/.ssh/specific_key
```

---

## Quick Reference

| Task | Command |
|------|---------|
| Generate key | `ssh-keygen -t ed25519` |
| Copy key to server | `ssh-copy-id user@server` |
| Connect | `ssh user@server` |
| Custom port | `ssh -p 2222 user@server` |
| Run command | `ssh user@server "command"` |
| Copy file to server | `scp file user@server:/path/` |
| Copy from server | `scp user@server:/path/file ./` |
| Local tunnel | `ssh -L 8080:localhost:80 user@server` |
| Jump host | `ssh -J bastion user@internal` |
| Debug | `ssh -v user@server` |

---

*SSH config file = never type long commands again.*
