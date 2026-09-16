---
name: tailscale-ssh-commands
description: >
  Discover devices on a Tailscale network, query device status, and execute remote commands
  or interactive shell sessions using native Tailscale SSH and standard SSH over WireGuard.
  Includes automation-safe flags, JSON status parsing with jq, host key management, and remote troubleshooting.
  Use when the user asks to "ssh to tailscale machine", "list tailscale devices", "run remote command on tailscale node",
  "tailscale ssh", or "check tailnet device status".
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment with Tailscale CLI and SSH"
license: MIT
---

# Tailscale Device Discovery & SSH Operations

A comprehensive runbook for discovering networked nodes, filtering online hosts, and executing remote commands securely over Tailscale using **Tailscale SSH** (zero-key credential management) and traditional SSH over WireGuard.

---

## Trigger Phrases

| User Input | Core Command / Flow |
|---|---|
| "List devices on my Tailscale network" | `tailscale status` / `tailscale status --json` |
| "SSH into my server using Tailscale" | `tailscale ssh <user>@<host>` |
| "Run a command on remote tailnet machine" | Non-interactive SSH command execution |
| "Find IP of tailscale device <host>" | `tailscale ip -4 <host>` |

---

## Why Use Tailscale SSH?

Traditional SSH requires distributing and rotating public keys in `~/.ssh/authorized_keys` across every machine.

**Tailscale SSH advantages:**
- **Zero Key Management:** Tailscale issues short-lived cryptographic credentials tied to your Tailscale identity.
- **Automatic Host Key Verification:** The CLI verifies host keys against the Tailscale coordination server, preventing "Host key verification failed" errors.
- **Centralized ACL Controls:** Control who can SSH into which machine (and as which OS user) directly from Tailscale Admin ACLs.

---

## 1. Network Discovery & Device Inspection

### Quick Human-Readable Status
```bash
# List all machines in your tailnet, connection state, and direct/relay link
tailscale status
```

### Automation Recipes with `tailscale status --json`
Inspect your tailnet programmatically using `jq`:

```bash
# List all online peers with Hostname, IPv4, and Operating System
tailscale status --json | jq -r '
  .Peer[] | select(.Online == true) | 
  "\(.HostName) \t \(.TailscaleIPs[0]) \t \(.OS)"
'

# Find the IPv4 of a specific hostname
tailscale ip -4 <hostname>

# Check if a specific host is actively connected
tailscale status --json | jq -r '
  .Peer[] | select(.HostName == "<hostname>") | .Online
'
```

---

## 2. Remote SSH Execution

### Method A: Native `tailscale ssh` (Recommended)
Automatically routes through MagicDNS, manages credentials, and authenticates without manual key distribution:

```bash
# Interactive shell
tailscale ssh <user>@<hostname>

# Run a single non-interactive command
tailscale ssh <user>@<hostname> "df -h && free -m"
```

### Method B: Standard OpenSSH over Tailscale
If standard OpenSSH is preferred, use automation-safe flags to prevent prompts from blocking agents or background scripts:

```bash
# Non-interactive execution with automatic host-key acceptance
ssh -o BatchMode=yes -o StrictHostKeyChecking=accept-new <user>@<hostname> '<remote-command>'
```

---

## 3. Practical Remote Inspection Recipes

```bash
# Check disk utilization on remote server
tailscale ssh user@my-server "df -h /"

# Inspect Docker container health
tailscale ssh user@my-server "docker ps --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'"

# Inspect systemd service logs (last 50 lines)
tailscale ssh user@my-server "journalctl -u my-app.service -n 50 --no-pager"

# Copy files via scp over Tailscale WireGuard link
scp -o StrictHostKeyChecking=accept-new ./build.tar.gz user@my-server:/opt/releases/

# Fast folder synchronization via rsync over Tailscale
rsync -avz -e "ssh -o StrictHostKeyChecking=accept-new" ./src/ user@my-server:/var/www/app/
```

---

## 4. Enabling Tailscale SSH on a Target Node

To allow incoming Tailscale SSH connections on a machine:

```bash
# Advertise Tailscale SSH capability on the target host
sudo tailscale up --ssh --accept-routes
```

### Sample ACL Configuration (`tailnet` Admin Console)
To grant access in your Tailscale ACL policy:

```jsonc
"ssh": [
  {
    // Grant users in autogroup:admin SSH access to all tagged servers
    "action": "check",       // "check" requires periodic web re-auth; "accept" permits passwordless
    "src":    ["autogroup:admin"],
    "dst":    ["tag:servers"],
    "users":  ["ubuntu", "root"]
  }
]
```

---

## 5. Troubleshooting Matrix

| Error / Symptom | Root Cause | Solution |
|---|---|---|
| `Host is offline` | Tailscale client stopped on target | Check target machine status with `tailscale status` |
| `Permission denied (publickey)` | Tailscale SSH not enabled on target | Target must run `sudo tailscale up --ssh` |
| Command hangs waiting for password | OpenSSH prompting for password in non-interactive shell | Pass `-o BatchMode=yes` or enable Tailscale SSH |
| `Access denied by ACL` | Target ACL policy restricts user or destination tag | Update SSH rules in Tailscale Admin ACLs |
