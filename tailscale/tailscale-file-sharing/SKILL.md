---
name: tailscale-file-sharing
description: Share files between Tailscale connected devices using Taildrop
---

## What I do

I help you share files between devices on your Tailscale network using Taildrop.

## Prerequisites

- Tailscale CLI installed and authenticated on both devices
- Devices must be on the same Tailscale network

## Send Files

Send a file to another device:

```bash
tailscale file cp <file> <target>
```

Target can be:
- hostname: `tailscale file cp myfile.txt my-laptop`
- Tailscale IP: `tailscale file cp myfile.txt 100.64.0.1`

Send multiple files:

```bash
tailscale file cp file1.txt file2.txt my-laptop
```

Send to a specific path on target:

```bash
tailscale file cp myfile.txt my-laptop:/home/user/documents/
```

## Receive Files

Files are received in:
```
~/Downloads/Tailscale/
```

List received files:

```bash
ls ~/Downloads/Tailscale/
```

## Check File Status

See pending file transfers:

```bash
tailscale file get
```

## Cancel Transfer

Cancel a pending file transfer:

```bash
tailscale file cancel <transfer-id>
```

## Limitations

- Maximum file size: 1GB per file
- Files expire after 7 days if not downloaded
- Both devices must be online simultaneously
- No resume capability for interrupted transfers

## Troubleshooting

If file transfer fails:
1. Verify both devices are online: `tailscale status`
2. Check target device name/IP is correct
3. Ensure sufficient disk space on target
4. Check Tailscale permissions in ACLs
