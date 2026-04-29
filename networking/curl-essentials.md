# curl Essentials

> Test APIs, download files, debug HTTP issues.

## Table of Contents
- [Basic Requests](#basic-requests)
- [Headers and Authentication](#headers-and-authentication)
- [Sending Data](#sending-data)
- [Debugging](#debugging)
- [Downloading](#downloading)
- [Common API Patterns](#common-api-patterns)

---

## Basic Requests

### GET Request

```bash
curl https://api.example.com/users
```

### With Output Formatting

```bash
# Pretty print JSON
curl https://api.example.com/users | jq .

# Silent mode (no progress bar)
curl -s https://api.example.com/users

# Silent but show errors
curl -sS https://api.example.com/users
```

### Follow Redirects

```bash
curl -L https://example.com
```

Without `-L`, you'll just see the redirect response.

---

## Headers and Authentication

### Custom Headers

```bash
curl -H "Content-Type: application/json" https://api.example.com
```

### Multiple Headers

```bash
curl -H "Content-Type: application/json" \
     -H "Accept: application/json" \
     https://api.example.com
```

### Bearer Token

```bash
curl -H "Authorization: Bearer YOUR_TOKEN" https://api.example.com/me
```

### Basic Auth

```bash
curl -u username:password https://api.example.com
```

### API Key in Header

```bash
curl -H "X-API-Key: YOUR_KEY" https://api.example.com
```

---

## Sending Data

### POST JSON

```bash
curl -X POST https://api.example.com/users \
     -H "Content-Type: application/json" \
     -d '{"name": "John", "email": "john@example.com"}'
```

### POST from File

```bash
curl -X POST https://api.example.com/users \
     -H "Content-Type: application/json" \
     -d @data.json
```

### Form Data

```bash
curl -X POST https://example.com/login \
     -d "username=john&password=secret"
```

### File Upload

```bash
curl -X POST https://api.example.com/upload \
     -F "file=@photo.jpg"
```

### PUT Request

```bash
curl -X PUT https://api.example.com/users/123 \
     -H "Content-Type: application/json" \
     -d '{"name": "Updated Name"}'
```

### DELETE Request

```bash
curl -X DELETE https://api.example.com/users/123
```

### PATCH Request

```bash
curl -X PATCH https://api.example.com/users/123 \
     -H "Content-Type: application/json" \
     -d '{"status": "active"}'
```

---

## Debugging

### Show Response Headers

```bash
curl -I https://example.com
```

### Show Everything (Verbose)

```bash
curl -v https://api.example.com
```

Shows: DNS resolution, connection, TLS handshake, request headers, response headers.

### Show Request and Response Headers

```bash
curl -i https://api.example.com
```

### Only HTTP Status Code

```bash
curl -s -o /dev/null -w "%{http_code}" https://api.example.com
```

### Response Time

```bash
curl -s -o /dev/null -w "Time: %{time_total}s\n" https://api.example.com
```

### Full Timing Breakdown

```bash
curl -s -o /dev/null -w "\
DNS:        %{time_namelookup}s\n\
Connect:    %{time_connect}s\n\
TLS:        %{time_appconnect}s\n\
Start:      %{time_starttransfer}s\n\
Total:      %{time_total}s\n" https://api.example.com
```

### Save Response to File

```bash
curl -o response.json https://api.example.com/data
```

---

## Downloading

### Download File

```bash
curl -O https://example.com/file.zip
```

`-O` saves with the remote filename.

### Download with Custom Name

```bash
curl -o myfile.zip https://example.com/file.zip
```

### Resume Interrupted Download

```bash
curl -C - -O https://example.com/largefile.zip
```

### Download Multiple Files

```bash
curl -O https://example.com/file1.zip \
     -O https://example.com/file2.zip
```

### Limit Download Speed

```bash
curl --limit-rate 1M -O https://example.com/largefile.zip
```

---

## Common API Patterns

### GitHub API

```bash
# Get user info
curl -s https://api.github.com/users/octocat | jq .

# With authentication
curl -H "Authorization: token YOUR_TOKEN" \
     https://api.github.com/user/repos
```

### REST API CRUD

```bash
# Create
curl -X POST https://api.example.com/items \
     -H "Content-Type: application/json" \
     -d '{"name": "New Item"}'

# Read
curl https://api.example.com/items/123

# Update
curl -X PUT https://api.example.com/items/123 \
     -H "Content-Type: application/json" \
     -d '{"name": "Updated Item"}'

# Delete
curl -X DELETE https://api.example.com/items/123
```

### Check if API is Up

```bash
curl -s -o /dev/null -w "%{http_code}" https://api.example.com/health
# Returns: 200
```

### Loop Through Pages

```bash
for page in {1..5}; do
  curl -s "https://api.example.com/items?page=$page" >> all_items.json
done
```

---

## Quick Reference

| Task | Command |
|------|---------|
| GET request | `curl URL` |
| POST JSON | `curl -X POST -H "Content-Type: application/json" -d '{}' URL` |
| Add header | `curl -H "Header: Value" URL` |
| Bearer auth | `curl -H "Authorization: Bearer TOKEN" URL` |
| Basic auth | `curl -u user:pass URL` |
| Follow redirects | `curl -L URL` |
| Silent | `curl -s URL` |
| Verbose | `curl -v URL` |
| Headers only | `curl -I URL` |
| Download | `curl -O URL` |
| Status code | `curl -s -o /dev/null -w "%{http_code}" URL` |

---

## Troubleshooting

### SSL Certificate Errors

> ⚠️ **NEVER use `-k` in production.** It disables SSL/TLS verification entirely — any attacker on the network can intercept and modify traffic. Use only in local dev with self-signed certs.

```bash
# LOCAL DEV ONLY — never in production, CI/CD, or against real APIs
curl -k https://self-signed.example.com
```

> **Better fix**: Add the self-signed cert to your system trust store instead:
> ```bash
> # macOS
> sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain cert.pem
> # curl with specific CA bundle (no global trust needed)
> curl --cacert ./my-ca.pem https://self-signed.example.com
> ```

### Timeout

```bash
# Set connection timeout
curl --connect-timeout 5 https://api.example.com

# Set max time for whole operation
curl --max-time 30 https://api.example.com
```

### Proxy

```bash
curl -x http://proxy:8080 https://api.example.com
```

### Save Cookies / Use Cookies

```bash
# Save cookies
curl -c cookies.txt https://example.com/login -d "user=john&pass=secret"

# Use cookies
curl -b cookies.txt https://example.com/dashboard
```

---

*curl = your HTTP Swiss Army knife.*
