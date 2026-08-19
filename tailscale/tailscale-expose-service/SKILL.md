---
name: tailscale-expose-service
description: Expose local services to the internet using Tailscale Funnel
---

## What I do

I help you expose local services to the internet using Tailscale Funnel.

## Prerequisites

- Tailscale v1.32 or later
- HTTPS enabled in Tailscale admin console
- MagicDNS enabled
- Service running on localhost

## Enable Funnel

Expose a local port to the internet:

```bash
tailscale funnel --bg <port>
```

Example - expose a web server on port 3000:

```bash
tailscale funnel --bg 3000
```

The service will be available at:
```
https://<your-device-name>.<tailnet-name>.ts.net
```

## Check Funnel Status

View active Funnel services:

```bash
tailscale funnel status
```

## Stop Funnel

Stop exposing a service:

```bash
tailscale funnel --off
```

## Advanced Options

Use a custom path:

```bash
tailscale funnel --bg --set-path /api 3000
```

Use a specific HTTPS cert:

```bash
tailscale funnel --bg --https 443 3000
```

## Expose Multiple Services

You can expose multiple services simultaneously using different paths:

```bash
# Web app on port 3000
tailscale funnel --bg 3000

# API on port 8080
tailscale funnel --bg --set-path /api 8080

# Database UI on port 5432
tailscale funnel --bg --set-path /admin 5432
```

Results:
- `https://your-device.tailnet.ts.net/` → port 3000
- `https://your-device.tailnet.ts.net/api` → port 8080
- `https://your-device.tailnet.ts.net/admin` → port 5432

View all active funnels:
```bash
tailscale funnel status
```

## Common Use Cases

Expose a development server:

```bash
tailscale funnel --bg 8080
```

Expose a database admin UI:

```bash
tailscale funnel --bg 5432
```

## Security Considerations

- Only expose services that need public access
- Use Tailscale ACLs to control access
- Monitor access logs regularly
- Disable Funnel when not needed: `tailscale funnel --off`

## Troubleshooting

If Funnel doesn't work:
1. Verify Tailscale version: `tailscale version`
2. Check HTTPS is enabled in admin console
3. Verify MagicDNS is enabled
4. Check firewall allows Tailscale traffic
5. Ensure service is running on localhost
