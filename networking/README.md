# Networking

> Debug connections, test APIs, troubleshoot network issues.

## Guides

| Guide | What's Inside |
|-------|---------------|
| [curl Essentials](curl-essentials.md) | HTTP requests, API testing |
| [SSH Mastery](ssh-mastery.md) | Keys, config, tunnels, jump hosts |
| [DNS & Ports](dns-ports.md) | Resolve names, manage ports |
| [Debugging](debugging.md) | Troubleshooting flowcharts + real scenarios |

---

## Quick Commands

```bash
# Check if port is open
nc -zv localhost 3000

# What's using a port
lsof -i :3000

# Test HTTP endpoint
curl -I https://example.com

# DNS lookup
dig example.com

# Your public IP
curl -s ifconfig.me
```

---

## Common Scenarios

| Problem | Start Here |
|---------|------------|
| Can't connect to localhost | Check if service is running |
| API returning errors | `curl -v` for details |
| SSH not working | Check key permissions |
| Container can't reach internet | Check Docker network |
