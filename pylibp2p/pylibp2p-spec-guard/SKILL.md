---
name: pylibp2p-spec-guard
description: >
  Enforce strict adherence to official libp2p specifications (https://github.com/libp2p/specs) across all
  code changes, wire protocols, multiaddrs, and modules in py-libp2p. Defaults to 100% canonical spec compliance.
  If a requested modification deviates from, modifies, or violates the official libp2p specification, intercepts
  the action, displays an explicit high-visibility warning detailing interoperability and network partition risks,
  and requires explicit user confirmation before proceeding.
  Use when making any architectural, protocol, wire, or behavioral changes to py-libp2p.
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment"
license: MIT
---

# py-libp2p Specification Compliance Guard & Deviation Interceptor

A strict architectural governance skill ensuring that all code modifications, protocol implementations, and wire-level behaviors in `py-libp2p` adhere 100% to the official **[libp2p specifications](https://github.com/libp2p/specs)**.

---

## 🎯 Core Operating Principle

> **Default Stance:** Every protocol ID, wire frame, state machine transition, timeout, cryptographic exchange, and multiplexing rule in `py-libp2p` MUST strictly conform to the canonical libp2p specifications.
>
> **The Golden Rule of Interoperability:** A libp2p node that deviates from the spec will fail to communicate with `go-libp2p`, `rust-libp2p`, and `js-libp2p`, causing network partitions or silent connection drops.
>
> **The Deviation Interceptor:** If the user or a task proposes changes that differ from the official spec, the agent **MUST NOT** implement them silently. The agent **MUST display an explicit warning** and proceed **ONLY IF** the user gives explicit, unambiguous permission.

---

## Trigger Phrases

| User Input | Action Taken |
|---|---|
| Any code change touching `libp2p/` modules | Silently check against official libp2p specs before editing |
| "Add custom framing / modified handshake" | Trigger Warning Interceptor: Halt & request explicit confirmation |
| "Check libp2p spec compliance" | Audit existing or proposed code against canonical libp2p RFCs |
| "Modify multistream / yamux / noise behavior" | Validate against spec bounds; warn if non-standard |

---

## 📚 Official libp2p Specification Mapping

Always verify requirements against the official specification documents:

| Module / Subsystem | Official Specification Reference | Canonical Protocol ID / Wire Invariants |
|---|---|---|
| **Addressing** | [libp2p/specs/addressing](https://github.com/libp2p/specs/blob/master/addressing/README.md) | Multiaddr binary & string formats, `/p2p/<PeerID>` |
| **Peer Identity** | [libp2p/specs/peer-ids](https://github.com/libp2p/specs/blob/master/peer-ids/peer-ids.md) | Multihash-derived PeerID; Ed25519 (RFC 0001), RSA, Secp256k1 |
| **Protocol Negotiation** | [libp2p/specs/connections](https://github.com/libp2p/specs/blob/master/connections/README.md) | `/multistream/1.0.0`, unsigned varint length + `\n` |
| **Security: Noise** | [libp2p/specs/noise](https://github.com/libp2p/specs/blob/master/noise/README.md) | `/noise`, Noise XX handshake pattern, 2-byte big-endian frame lengths |
| **Security: TLS 1.3** | [libp2p/specs/tls](https://github.com/libp2p/specs/blob/master/tls/tls.md) | `/tls/1.3.0`, libp2p public key certificate extension |
| **Stream Muxing: Yamux** | [libp2p/specs/yamux](https://github.com/libp2p/specs/blob/master/yamux/README.md) | `/yamux/1.0.0`, 12-byte header, window update credit flow control |
| **Stream Muxing: Mplex** | [libp2p/specs/mplex](https://github.com/libp2p/specs/blob/master/mplex/README.md) | `/mplex/6.7.0`, varint header (header = (id << 3) \| flag) |
| **Identify & Identify Push** | [libp2p/specs/identify](https://github.com/libp2p/specs/blob/master/identify/README.md) | `/libp2p/id/1.0.0`, `/libp2p/id/push/1.0.0`, Protobuf schema |
| **Ping** | [libp2p/specs/ping](https://github.com/libp2p/specs/blob/master/ping/ping.md) | `/ipfs/ping/1.0.0`, exactly 32 random bytes echoed back |
| **AutoNAT & AutoNAT v2** | [libp2p/specs/autonat](https://github.com/libp2p/specs/blob/master/autonat/README.md) | `/libp2p/autonat/1.0.0`, dial-back verification |
| **Circuit Relay v2** | [libp2p/specs/relay](https://github.com/libp2p/specs/blob/master/relay/circuit-v2.md) | `/libp2p/circuit/relay/0.2.0/hop`, `/stop`, reservation limits |
| **Kademlia DHT** | [libp2p/specs/kad-dht](https://github.com/libp2p/specs/blob/master/kad-dht/README.md) | `/ipfs/kad/1.0.0`, `/pk/`, `/ipns/`, `/providers/` namespaces |
| **PubSub / GossipSub** | [libp2p/specs/pubsub](https://github.com/libp2p/specs/blob/master/pubsub/gossipsub/gossipsub-v1.1.md) | `/meshsub/1.1.0`, `/meshsub/1.0.0`, IHAVE/IWANT/PRUNE/GRAFT, peer scoring |
| **Bitswap** | [libp2p/specs/bitswap](https://github.com/libp2p/specs/blob/master/bitswap/README.md) | `/ipfs/bitswap/1.2.0`, `/ipfs/bitswap/1.1.0`, want-lists & blocks |

---

## 🛡️ The 4-Step Verification & Interception Workflow

```
[User Request / Code Edit]
           │
           ▼
[Step 1: Check against libp2p Specs]
           │
     Is it compliant?
     ├── YES ──► Proceed with implementation (Step 4A)
     │
     └── NO / DEVIATION DETECTED
           │
           ▼
[Step 2: HALT & Render Warning Interceptor]
           │
[Step 3: Await Explicit User Permission]
     ├── User Confirms Deviation ──► Proceed with marked custom code (Step 4B)
     └── User Wants Spec Compliance ──► Implement strictly per spec (Step 4A)
```

---

### Step 1: Pre-Implementation Spec Audit
Before modifying any code in `py-libp2p`:
1. Identify which libp2p subsystem is affected.
2. Check the canonical spec requirements for:
   - Protocol ID string (e.g. `/ipfs/ping/1.0.0`).
   - Frame layout, byte ordering (endianness), and length prefixing.
   - Expected error conditions, reset flags, and close handshakes.
   - Message Protobuf schemas and field tags.
3. Compare the proposed change with the spec:
   - Does this change alter wire bytes?
   - Does this change skip required validation (e.g. signature check in GossipSub)?
   - Does this change add a non-standard header or custom protocol ID?
   - Would a `go-libp2p` or `rust-libp2p` peer fail to communicate with this?

---

### Step 2: The Warning Interceptor (When Deviation is Detected)

If a change is non-compliant, **STOP IMMEDIATELY**. Render this exact warning modal:

```markdown
> [!WARNING]
> ### ⚠️ libp2p Specification Deviation Detected
>
> You have requested a change that deviates from the official **libp2p specification**.
>
> - **Subsystem / Module:** `<e.g., Stream Muxer (Yamux) / Identify / multistream-select>`
> - **Official libp2p Spec:** [`<Spec Title>`](<https://github.com/libp2p/specs/...>)
> - **What the Spec Mandates:**
>   `<Concise explanation of what the official standard requires, e.g.: "The Yamux spec requires a 12-byte header with big-endian length and 0x0004 for FIN.">`
> - **Proposed Deviation:**
>   `<What is being changed that differs, e.g.: "Omitting FIN frames on stream teardown and using custom 8-byte headers.">`
> - **Interoperability & Network Risk:**
>   `<Concrete consequence, e.g.: "This will cause immediate connection desynchronization and StreamReset errors when communicating with go-libp2p, rust-libp2p, or Kubo nodes.">`
>
> ---
>
> 🛑 **Explicit Permission Required:**
> Default policy requires all changes to be 100% spec-compliant. 
> Do you explicitly approve deviating from the official libp2p specification for this change?
>
> - **Option 1 (Recommended):** Keep it 100% compliant with the official libp2p spec.
> - **Option 2:** Proceed with the custom deviation (I accept breaking interop).
```

---

### Step 3: Await User Instruction
- **NEVER** assume permission.
- **NEVER** start editing files until the user replies.
- If the user selects **Option 1** (or asks to stick to the spec): Implement the standard spec behavior.
- If the user selects **Option 2** (explicitly confirms the deviation): Proceed, but execute Step 4B.

---

### Step 4B: Documenting Approved Deviations
When an out-of-spec change is explicitly authorized by the user:
1. Clearly mark the deviating code with a standard deviation comment:
   ```python
   # ⚠️ SPEC-DEVIATION [AUTHORIZED BY USER]:
   # Official libp2p spec mandates: <standard requirement>
   # Custom deviation: <why this custom behavior was implemented>
   ```
2. Note the deviation in the PR newsfragment under `breaking.rst` or `internal.rst`.

---

## 📋 Non-Negotiable Compliance Invariants

1. **Protocol Strings are Sacred:** Never invent custom protocol IDs for standard protocols (e.g. do not rename `/ipfs/ping/1.0.0` to `/libp2p/ping/2.0`).
2. **Framing Must Match Wire Bytes:** Never change big-endian to little-endian or replace unsigned varints with fixed ints unless specified in a new versioned spec.
3. **Protobuf Backward Compatibility:** Never change existing field numbers in `.proto` files; only add new optional fields.
4. **Silence is Failure:** If a peer violates the spec on the wire, handle it with an explicit protocol error or stream reset — never silently adapt or corrupt local state.
