---
name: tailscale-expose-service
description: >
  Expose local web applications, APIs, databases, or Unix domain sockets securely using Tailscale Serve
  (for private sharing within your tailnet) or Tailscale Funnel (for public sharing to the open internet).
  Guides configuration of ports, custom paths, TLS termination, background execution, and ACL permissions.
  Use when the user asks to "expose a local port", "share my dev server with Tailscale", "set up Tailscale Funnel",
  "share localhost with tailnet", "tailscale serve", or "make localhost public with Tailscale".
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment with Tailscale CLI"
license: MIT
---

# Tailscale Serve & Funnel: Expose Local Services

A complete operational guide for sharing local development servers, APIs, and background daemons using **Tailscale Serve** (private to your tailnet) and **Tailscale Funnel** (public to the open internet) with automatic HTTPS certificates and zero port-forwarding router configuration.

---

## Trigger Phrases

| User Input | Recommended Command | Access Scope |
|---|---|---|
| "Share port 3000 on my private tailnet" | `tailscale serve` | Tailnet devices only (Private & Secure) |
| "Expose localhost:8080 to the public internet" | `tailscale funnel` | Anyone with the public URL (Internet) |
| "Show active funnel / serve endpoints" | `tailscale serve status` / `funnel status` | Diagnostic |
| "Stop sharing my local service" | `tailscale serve reset` / `funnel reset` | Teardown |

---

## Key Distinction: Serve vs Funnel

| Feature | `tailscale serve` | `tailscale funnel` |
|---|---|---|
| **Access Scope** | **Private**: Only authenticated devices in your Tailnet | **Public**: Anyone on the open World Wide Web |
| **Authentication** | WireGuard identity + Tailscale ACL rules | Unauthenticated public HTTP/HTTPS traffic |
| **Prerequisites** | Tailscale CLI installed and connected | MagicDNS + HTTPS Certs + ACL `"funnel"` attribute enabled |
| **Best For** | Internal staging, private DB UIs, peer review | Webhook endpoints, client demos, public testing |

---

## 1. Tailscale Serve (Private Tailnet Sharing)

Share a service running on `localhost:3000` with other devices on your Tailnet:

```bash
# Run in background (non-interactive, safe for automated environments)
tailscale serve --bg --yes 3000
```

The service is now reachable from any device on your Tailnet at:
```text
https://<device-name>.<tailnet-name>.ts.net
```

### Advanced Serve Recipes

#### Route Multiple Services with Custom URL Paths
```bash
# Frontend root on port 3000
tailscale serve --bg --yes 3000

# Backend API on /api routed to port 8080
tailscale serve --bg --yes --set-path /api 8080

# Admin UI on /admin routed to port 5432
tailscale serve --bg --yes --set-path /admin 5432
```

#### Proxy a Unix Domain Socket
```bash
tailscale serve --bg --yes unix:/var/run/docker.sock
```

#### Proxy a Self-Signed / Local HTTPS Service
```bash
tailscale serve --bg --yes https+insecure://localhost:8443
```

#### Raw TCP Forwarding (e.g., PostgreSQL or Redis)
```bash
tailscale serve --bg --yes --tcp 5432 127.0.0.1:5432
```

---

## 2. Tailscale Funnel (Public Internet Sharing)

Expose a local port to the entire internet through Tailscale's edge relays:

```bash
# Expose port 3000 publicly in the background
tailscale funnel --bg --yes 3000
```

Accessible publicly from any browser at:
```text
https://<device-name>.<tailnet-name>.ts.net
```

### Advanced Funnel Recipes

#### Expose a Specific Path Publicly
```bash
# Route public webhooks to local handler on port 9000
tailscale funnel --bg --yes --set-path /webhook 9000
```

---

## 3. Status, Inspection & Teardown

### View Active Configuration
```bash
# Check Serve configuration
tailscale serve status

# Check Funnel configuration
tailscale funnel status

# Get machine-readable JSON format
tailscale serve status --json
```

### Stop Sharing & Teardown
```bash
# Reset and stop all active Funnel endpoints
tailscale funnel reset

# Reset and stop all active Serve endpoints
tailscale serve reset
```

---

## 4. Prerequisites & ACL Configuration

If `tailscale funnel` reports an authorization error (`Funnel not enabled for this node`), configure your Tailscale ACL policy in the [Tailscale Admin Console](https://login.tailscale.com/admin/acls):

1. **Enable HTTPS Certificates:** In Tailnet Settings -> DNS -> Enable HTTPS Certificates.
2. **Enable MagicDNS:** In Tailnet Settings -> DNS -> Enable MagicDNS.
3. **Configure Funnel Node Attributes in ACLs:**

```jsonc
// Add to your Tailscale Access Control Policy (ACLs)
"nodeAttrs": [
  {
    "target": ["autogroup:members"],
    "attr": ["funnel"]
  }
]
```

---

## 5. Troubleshooting Matrix

| Issue | Root Cause | Solution |
|---|---|---|
| `Funnel is not permitted` | Missing `"funnel"` attribute in ACLs | Add `"funnel"` to `nodeAttrs` in Tailscale admin console |
| `502 Bad Gateway` | Local service not running or bound strictly to `127.0.0.1` | Verify local service: `curl http://localhost:<port>` |
| `TLS certificate error` | HTTPS not enabled in Tailnet DNS settings | Enable "HTTPS Certificates" in Tailscale Admin -> DNS |
| Command hangs waiting for input | Running interactively in terminal | Always supply the `--yes` flag: `tailscale serve --bg --yes <port>` |
