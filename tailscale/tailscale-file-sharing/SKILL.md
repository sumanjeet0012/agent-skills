---
name: tailscale-file-sharing
description: >
  Send and receive files peer-to-peer across Tailscale-connected devices using Taildrop (`tailscale file`).
  Covers target syntax, streaming stdin, managing inbox spools, conflict resolution policies,
  and handling OS-specific receiving behaviors (macOS GUI vs Linux CLI).
  Use when the user asks to "send file with tailscale", "share file to tailscale node",
  "taildrop", "tailscale file cp", or "receive files with tailscale".
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment with Tailscale CLI"
license: MIT
---

# Taildrop: Peer-to-Peer File Sharing with Tailscale

Taildrop (`tailscale file`) enables encrypted, direct peer-to-peer file transfers between authenticated devices on your Tailnet. Files are transferred over the WireGuard mesh with zero relay file storage and no third-party cloud upload.

---

## Trigger Phrases

| User Input | Core Command |
|---|---|
| "Send this file to my laptop with Tailscale" | `tailscale file cp <file> <target>:` |
| "Download files waiting in my Tailscale inbox" | `tailscale file get <target-dir>` |
| "List devices I can send files to" | `tailscale file cp --targets` |
| "Stream output directly to another machine" | `... | tailscale file cp --name=<name> - <target>:` |

---

## 1. Sending Files (`tailscale file cp`)

### Basic File Transfer
> [!IMPORTANT]
> The target host MUST end with a trailing colon (`:`). Taildrop sends files directly to the remote device's secure inbox (custom remote paths are not permitted by Taildrop security design).

```bash
# Discover eligible file transfer targets on your tailnet
tailscale file cp --targets

# Send a single file
tailscale file cp ./archive.tar.gz my-laptop:

# Send multiple files in a single batch
tailscale file cp doc1.pdf doc2.pdf image.png my-server:
```

### Stream Standard Input (Piping)
Send command output or compressed archives directly to a remote machine without creating a temporary local file:

```bash
# Pipe database dump directly to backup server
pg_dump mydb | tailscale file cp --name=mydb_backup.sql - backup-node:

# Pipe tar archive directly
tar -czf - ./project/ | tailscale file cp --name=project_bundle.tar.gz - dev-box:
```

---

## 2. Receiving Files (`tailscale file get`)

Receiving behavior depends on the operating system:

### On Linux (CLI & Headless Servers)
On Linux systems, incoming files are buffered in the local Tailscale daemon inbox until explicitly retrieved:

```bash
# Move all incoming files into the current working directory
tailscale file get .

# Move files into a specific target folder
tailscale file get ~/received_files/

# Overwrite existing files if filenames conflict
tailscale file get --conflict=overwrite ~/downloads/

# Wait for an incoming file if the inbox is currently empty
tailscale file get --wait ~/received_files/

# Run daemon/loop to continuously receive incoming files as they arrive
tailscale file get --loop ~/incoming/
```

### On macOS
- If the Tailscale standalone macOS app is installed, incoming transfers display a system notification and files are automatically placed in `~/Downloads`.
- If using `tailscaled` CLI on macOS, use `tailscale file get <target-directory>`.

### On Windows
- Files are saved to the user's `Downloads` folder automatically upon approval.

---

## 3. Conflict Resolution Options

When retrieving files with `tailscale file get`, control file overwrite behavior via `--conflict`:

| Flag Option | Behavior |
|---|---|
| `--conflict=skip` (default) | Skips conflicting files, leaving them safe in the inbox |
| `--conflict=overwrite` | Replaces the local file with the incoming version |
| `--conflict=rename` | Renames the incoming file with a numeric suffix (e.g., `file-1.pdf`) |

---

## 4. Taildrop Constraints & Specifications

- **File Size Limits:** No hard protocol file size limit (tested beyond multiple gigabytes, constrained by available disk space).
- **Inbox Expiration:** Spooled files remain available in the inbox for **7 days** before being discarded if not fetched.
- **Concurrent State:** Both machines must be online and connected to the Tailnet during the initial transfer handoff.
- **Encryption:** Fully end-to-end encrypted across WireGuard tunnels.

---

## 5. Troubleshooting Matrix

| Issue | Cause | Solution |
|---|---|---|
| `host not found` | Missing trailing colon in target | Ensure syntax includes colon: `tailscale file cp file.txt host:` |
| `transfer timed out` | Target machine is offline or in sleep mode | Verify online state with `tailscale status` |
| `no incoming files` on Linux | Files haven't been pulled from inbox | Run `tailscale file get .` to extract from spool |
| Permission denied writing files | Insufficient permissions on target directory | Ensure destination directory is writable by user |
