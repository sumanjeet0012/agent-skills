---
name: pylibp2p-wire-debug
description: >
  Debug wire-level protocols, network handshakes, multistream-select negotiation, Noise XX crypto handshakes,
  Yamux frame headers, and go-libp2p interoperability tests in py-libp2p.
  Use when debugging failed dials, dropped peer connections, multistream negotiation errors,
  Noise authentication failures, stream muxer desynchronization, or running interop tests against go-libp2p.
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment"
license: MIT
---

# py-libp2p Wire Protocol & Interop Debugger

A diagnostic runbook for inspecting raw packet bytes, multistream-select handshakes, Noise encrypted sessions, Yamux stream framing, and cross-implementation interoperability against `go-libp2p`.

---

## Trigger Phrases

| User Input | Diagnostic Domain |
|---|---|
| "Debug py-libp2p connection drop" | Full wire trace: Dial -> Multiaddr -> Security -> Muxer |
| "Multistream select negotiation failed" | Inspect `/multistream/1.0.0` protocol negotiation and varint headers |
| "Noise handshake failing in py-libp2p" | Noise XX 3-step handshake, ephemeral keys, and peer ID authentication |
| "Yamux frame error or window exhausted" | 12-byte Yamux frame header decoding and window update tracking |
| "Run go-libp2p interop tests" | Execute and debug tests in `interop/` directory |

---

## 1. Connection Handshake Pipeline

Every libp2p connection undergoes a strict multi-layer handshake:

```
[TCP / Transport Connect]
           │
           ▼
[multistream-select: /multistream/1.0.0]
           │
           ▼
[Negotiate Security: /noise] ──────────► [Noise XX Handshake & Peer Auth]
                                                         │
                                                         ▼
                                       [multistream-select: /yamux/1.0.0]
                                                         │
                                                         ▼
                                       [Yamux Muxed Streams Active]
                                                         │
                                                         ▼
                                       [Negotiate Protocol: /ipfs/ping/1.0.0]
```

---

## 2. multistream-select Wire Format

All protocol negotiation begins with length-prefixed protocol strings:

- **Format:** `<unsigned-varint-length><protocol-string>\n`
- **Example:** `/multistream/1.0.0\n` is 19 bytes long:
  ```hex
  13 2f 6d 75 6c 74 69 73 74 72 65 61 6d 2f 31 2e 30 2e 30 0a
  │  └─────────────────── protocol string ───────────────────┘ └─ newline
  └─ varint length (0x13 = 19)
  ```
- **Responses:**
  - `protocol-string\n`: Protocol supported and accepted.
  - `na\n`: Not available (reject protocol proposal).
  - `ls\n`: List available registered protocols.

### Diagnostic Tip
If negotiation hangs, verify that the sender sends the trailing `\n` (`0x0a`) and that the receiver is not stuck awaiting more bytes without flushing the buffer.

---

## 3. Noise XX Handshake Inspection

`py-libp2p` uses the **Noise XX** handshake pattern, where neither party knows the other's static key initially:

```
Message 1 (Initiator -> Responder):
-> e
(Sends 32-byte ephemeral public key)

Message 2 (Responder -> Initiator):
<- e, ee, s, es
(Sends responder ephemeral key, DH(ee), encrypted responder static key, DH(es))

Message 3 (Initiator -> Responder):
-> s, se
(Sends encrypted initiator static key, DH(se))
```

### Common Failure Points:
1. **PeerID Mismatch:** Remote peer static public key does not hash to the expected `PeerID` in the multiaddr.
2. **Clock Skew / Replay:** Bad authentication tags if keys or pre-shared parameters mismatch.
3. **Prologue Discrepancy:** The prologue string in libp2p Noise MUST match between client and server (usually empty or session-bound).

---

## 4. Yamux 12-Byte Frame Decoding

Yamux multiplexes multiple streams over a single connection using a fixed 12-byte header:

```
+---------------+---------------+---------------+---------------+
|  Version (1B) |   Type (1B)   |          Flags (2B)           |
+---------------+---------------+---------------+---------------+
|                          StreamID (4B)                        |
+---------------+---------------+---------------+---------------+
|                          Length (4B)                          |
+---------------+---------------+---------------+---------------+
```

### Type Codes:
- `0x00` — **DATA:** Payload follows header (length = payload bytes).
- `0x01` — **WINDOW_UPDATE:** Credit window update (length = bytes added to window).
- `0x02` — **PING:** Keep-alive ping (length = opaque nonce).
- `0x03` — **GO_AWAY:** Connection shutdown (length = error code).

### Flag Bits:
- `0x0001` — **SYN:** Open new stream (odd stream IDs for client, even for server).
- `0x0002` — **ACK:** Acknowledge stream creation.
- `0x0004` — **FIN:** Half-close (sender has finished writing).
- `0x0008` — **RST:** Abortive reset (terminate stream immediately).

### Rapid Diagnostic Table:
| Symptom | Root Cause | Fix |
|---|---|---|
| Stream hangs on write | Send window exhausted | Ensure receiver sends `WINDOW_UPDATE` as buffers drain |
| Stream throws `StreamReset` | Peer sent Yamux `RST` flag | Check peer logs for unhandled exceptions or timeouts |
| Corrupted payload data | Short read on Yamux header | Use `read_exactly(stream, 12)` to parse header |

---

## 5. Running Interop Tests against go-libp2p

`py-libp2p` includes an interoperability test suite testing compatibility against `go-libp2p` binaries:

```bash
# Activate venv
source .venv/bin/activate || source venv/bin/activate

# Run all interop tests
pytest tests/interop/ -v

# Run with verbose debug logging for wire frames
pytest tests/interop/ -v -s --log-cli-level=DEBUG
```

### Key Checks for Interop PRs:
1. **End-to-end multiaddr format:** Ensure addresses parse identically in Go and Python.
2. **Buffer flushing:** Ensure writes are immediately flushed to the transport socket so Go peers aren't left waiting on buffering.
3. **Graceful EOF:** Confirm half-closed streams in Python send valid Yamux `FIN` frames that `go-libp2p` interprets as `io.EOF`.
