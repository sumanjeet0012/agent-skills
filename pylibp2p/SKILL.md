---
name: pylibp2p
description: >
  Master router and architectural reference for developing, debugging, and maintaining the py-libp2p repository.
  Guides py-libp2p PR pre-flight validation, newsfragments, Trio structured concurrency auditing,
  protocol scaffolding, multistream-select, Noise security handshakes, Yamux stream muxing, and interop testing.
  Use when the user asks for "py-libp2p help", "work on py-libp2p", "prepare py-libp2p PR",
  "debug py-libp2p", "audit py-libp2p concurrency", or any task inside the py-libp2p codebase.
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment"
license: MIT
---

# py-libp2p Master Operational Suite

A dedicated engineering toolkit for developing, auditing, and maintaining **`py-libp2p`** — the official Python implementation of the libp2p modular peer-to-peer networking stack.

---

## 🏛️ py-libp2p Architectural Layers

```
┌────────────────────────────────────────────────────────┐
│  Application Protocols: Ping, Identify, DHT, Bitswap   │
├────────────────────────────────────────────────────────┤
│  PubSub Subsystems: GossipSub, FloodSub                │
├────────────────────────────────────────────────────────┤
│  Stream Multiplexing: Yamux, Mplex                     │
├────────────────────────────────────────────────────────┤
│  Secure Channel: Noise (XX pattern), TLS               │
├────────────────────────────────────────────────────────┤
│  Transports: TCP, QUIC, WebSocket                      │
├────────────────────────────────────────────────────────┤
│  Network Swarm, Peerstore & PeerID                     │
└────────────────────────────────────────────────────────┘
```

> **Golden Architectural Rule:** Lower layers must NEVER import from or depend on upper layers. Any upward dependency (e.g., Transport importing from PubSub or Host) is a critical architecture violation.

---

## 🛠️ Specialized py-libp2p Skills

| Skill | Path | Primary Purpose |
|---|---|---|
| **Spec Compliance Guard** | [pylibp2p-spec-guard](./pylibp2p-spec-guard/SKILL.md) | Enforces 100% official libp2p spec compliance by default; intercepts any out-of-spec changes with explicit warning and requires user confirmation. |
| **PR Pre-Flight & Newsfragments** | [pylibp2p-pr-prep](./pylibp2p-pr-prep/SKILL.md) | Validates mandatory towncrier newsfragments, executes `make pr`, checks Sphinx docs, and verifies interface ABI compatibility. |
| **Concurrency & Lifecycle Auditor** | [pylibp2p-concurrency-audit](./pylibp2p-concurrency-audit/SKILL.md) | Audits Trio structured concurrency, task leaks, cancellation swallow traps (`trio.Cancelled`), and stream short-read bugs (`read_exactly`). |
| **Protocol Scaffolder** | [pylibp2p-protocol-scaffolder](./pylibp2p-protocol-scaffolder/SKILL.md) | Scaffolds new libp2p protocols: Protocol ID, stream handlers, protobuf framing, client methods, and `@pytest.mark.trio` integration tests. |
| **Wire Protocol & Interop Debugger** | [pylibp2p-wire-debug](./pylibp2p-wire-debug/SKILL.md) | Decodes raw packets, multistream-select varint headers, Noise XX handshakes, Yamux 12-byte frames, and runs `go-libp2p` interop tests. |

---

## ⚡ Daily Development Cheat Sheet

### 1. Build, Lint & CI Pre-Flight
```bash
# Activate virtual environment
source .venv/bin/activate || source venv/bin/activate

# Full PR validation (clean -> fix -> lint -> typecheck -> test)
make pr

# Autoformat code with ruff
make fix

# Validate mandatory newsfragments before pushing
python ./newsfragments/validate_files.py
towncrier build --draft --version preview

# Recompile protocol buffers if .proto files changed
make protobufs
```

### 2. Fast Targeted Testing
```bash
# Run a specific test module
pytest tests/core/host/test_ping.py -v

# Run with full async debug logs to trace packet flows
pytest tests/core/stream_muxer/test_yamux.py -v -s --log-cli-level=DEBUG

# Run cross-implementation interop tests against go-libp2p
pytest tests/interop/ -v
```

### 3. Concurrency & Stream Golden Invariants
- **Always use `read_exactly(stream, n)`** when expecting a fixed-length header; `stream.read(n)` is a short read.
- **Never swallow `trio.Cancelled` or `asyncio.CancelledError`** — always re-raise.
- **Always handle `StreamEOF` and `StreamReset`** gracefully in protocol loops.
