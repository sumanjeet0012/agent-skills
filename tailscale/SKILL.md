---
name: tailscale
description: >
  Unified Tailscale network operations router and master runbook.
  Guides secure device connectivity, Tailscale SSH execution, peer-to-peer file transfer (Taildrop),
  private service sharing (Tailscale Serve), and public internet publishing (Tailscale Funnel).
  Use when the user asks for general "tailscale help", "connect to tailscale", "tailscale status",
  "tailscale setup", or wants to perform any administrative task across their Tailnet.
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment with Tailscale CLI"
license: MIT
---

# Tailscale Master Operational Router

Tailscale creates a secure, encrypted WireGuard mesh network connecting your personal computers, cloud virtual machines, containers, and development environments with zero firewall configuration.

---

## Skill Directory & Capabilities

This suite provides three specialized operational skills:

| Capability | Dedicated Skill | Core Command | Primary Use Case |
|---|---|---|---|
| **Expose Services** | [tailscale-expose-service](./tailscale-expose-service/SKILL.md) | `tailscale serve` / `funnel` | Share local web servers, APIs, or databases privately on Tailnet or publicly |
| **Remote SSH** | [tailscale-ssh-commands](./tailscale-ssh-commands/SKILL.md) | `tailscale ssh <host>` | Execute commands and shell sessions on remote nodes without managing keys |
| **File Sharing** | [tailscale-file-sharing](./tailscale-file-sharing/SKILL.md) | `tailscale file cp / get` | Direct peer-to-peer encrypted file transfers via Taildrop |

---

## Quick Command Cheat Sheet

### 1. Connection & Daemon Management
```bash
# Connect machine to Tailscale
tailscale up

# Check connection status & list connected nodes
tailscale status

# Check latency / WireGuard ping to a peer
tailscale ping <peer-name-or-ip>

# Disconnect from Tailscale
tailscale down
```

### 2. Node Inspection & IP Retrieval
```bash
# Get your own node's Tailscale IPv4 address
tailscale ip -4

# Get the IPv4 address of another device
tailscale ip -4 <device-hostname>
```

### 3. Sharing Services (Serve vs Funnel)
```bash
# Share port 3000 privately within your Tailnet only
tailscale serve --bg --yes 3000

# Expose port 3000 publicly to the open internet
tailscale funnel --bg --yes 3000

# Inspect active sharing configurations
tailscale serve status
tailscale funnel status
```

### 4. Remote Execution & Taildrop
```bash
# SSH into a remote node without SSH keys
tailscale ssh <user>@<device-hostname>

# Send a file to a remote node
tailscale file cp ./document.pdf <device-hostname>:

# Fetch incoming files from inbox on Linux
tailscale file get .
```

---

## Diagnostic Triage

1. **Verify daemon status:** Run `tailscale status`. If `tailscaled` is not running, start it:
   - macOS: Open the Tailscale app or run `sudo tailscaled`
   - Linux: `sudo systemctl restart tailscaled`
2. **Verify peer reachability:** Run `tailscale ping <peer>`. A direct WireGuard link is preferred; DERP relays are used if NAT traversal fails.
3. **MagicDNS resolution:** Verify that `<hostname>` resolves to `<hostname>.<tailnet>.ts.net`.
