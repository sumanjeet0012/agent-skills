---
name: tailscale-ssh-commands
description: Discover Tailscale devices and execute SSH commands on them
---

## What I do

I help you discover devices on your Tailscale network and execute SSH commands on them.

## Prerequisites

- Tailscale CLI installed and authenticated
- SSH access enabled on target devices
- Tailscale SSH keys configured (if using Tailscale SSH)

## Discover Devices

List all devices on your Tailscale network:

```bash
tailscale status
```

Get detailed status in JSON format:

```bash
tailscale status --json
```

## Find Device IP

Get the Tailscale IP of a device:

```bash
tailscale ip -4 <hostname>
```

## SSH to Device

SSH using Tailscale hostname:

```bash
ssh <user>@<hostname>
```

SSH using Tailscale IP:

```bash
ssh <user>@<tailscale-ip>
```

## Execute Remote Commands

Run a command on a remote device:

```bash
ssh <user>@<hostname> '<command>'
```

Example - check disk usage:

```bash
ssh user@my-server 'df -h'
```

Example - list running services:

```bash
ssh user@my-server 'systemctl list-units --type=service --state=running'
```

## Tailscale SSH (if enabled)

If Tailscale SSH is enabled on the target device:

```bash
tailscale ssh <user>@<hostname>
```

## Troubleshooting

If SSH fails:
1. Verify device is online: `tailscale status`
2. Check if SSH is allowed in Tailscale ACLs
3. Ensure SSH service is running on target device
4. Check firewall rules on target device
