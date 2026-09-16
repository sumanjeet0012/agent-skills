---
name: pylibp2p-concurrency-audit
description: >
  Audit py-libp2p code for Trio structured concurrency bugs, task leaks, cancellation swallow traps,
  stream lifecycle leaks, short-read truncation bugs, and resource manager (rcmgr) violations.
  Use when reviewing, debugging, or writing networking, transport, stream muxer (Yamux/Mplex),
  or protocol handlers in py-libp2p. Triggers on "audit concurrency in py-libp2p", "check task leaks",
  "Trio cancellation check", "debug stream closing", or "check py-libp2p async code".
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment"
license: MIT
---

# py-libp2p Concurrency & Stream Lifecycle Auditor

`py-libp2p` relies on structured concurrency (powered by **Trio** and `anyio`). Concurrency bugs in decentralized peer-to-peer networks cause hung streams, silent task leaks, corrupted protocol frames, and dropped connections under high churn.

This skill audits code changes against the six critical concurrency invariants of `py-libp2p`.

---

## Trigger Phrases

| User Input | Audit Focus |
|---|---|
| "Audit concurrency in this py-libp2p module" | Comprehensive check against the 6 core invariants |
| "Check for task leaks or cancellation bugs" | Nursery scope, `trio.Cancelled` re-raising, and task teardown |
| "Why is this stream hanging or truncated?" | `read_exactly` vs `stream.read()` short-read diagnosis |
| "Audit resource manager / stream closing" | `StreamEOF`, `StreamReset`, and `rcmgr` scope release |

---

## The 6 Concurrency Invariants of py-libp2p

### 1. The Short-Read Trap (`read_exactly` vs `stream.read`)
> [!CRITICAL]
> `stream.read(n)` in both Mplex and Yamux is a **short read**. It returns *up to* $n$ bytes currently available in the buffer, NOT exactly $n$ bytes.

If a peer delivers a 32-byte hash or 12-byte Yamux header across multiple TCP segments, `stream.read(32)` will return fewer than 32 bytes and silently truncate the payload!

- ❌ **Flawed Code:**
  ```python
  payload = await stream.read(32)  # May return 14 bytes! Truncation bug!
  ```
- ✅ **Correct Code:**
  ```python
  from libp2p.io.utils import read_exactly

  payload = await read_exactly(stream, 32)  # Guaranteed to block until all 32 bytes arrive
  ```

---

### 2. Mandatory Cancellation Propagation (`trio.Cancelled`)
Neither `trio.Cancelled` nor `asyncio.CancelledError` may EVER be silently caught or swallowed. Swallowing cancellation prevents peer disconnects and deadlocks the nursery during shutdown.

- ❌ **Flawed Code:**
  ```python
  try:
      await stream.write(data)
  except Exception as e:  # In Python < 3.11 or unhandled BaseException, catches Cancelled!
      logger.warning("Failed write: %s", e)
  ```
- ✅ **Correct Code:**
  ```python
  import trio

  try:
      await stream.write(data)
  except trio.Cancelled:
      # Perform only non-blocking synchronous cleanup, then ALWAYS re-raise
      raise
  except Exception as e:
      logger.warning("Failed write: %s", e)
  ```

---

### 3. Structured Task Nurseries (No Leaked Tasks)
All background coroutines must be spawned within a tracked nursery or explicitly tracked task list that is cancelled and awaited in `close()`.

- **Rules:**
  - Tasks spawned on a connection must terminate when the connection closes.
  - Background workers (e.g. heartbeat loops, gossipsub maintenance) must be bound to `self.nursery` or checked against a cancellation scope:
    ```python
    with trio.open_nursery() as nursery:
        nursery.start_soon(self._read_loop)
        nursery.start_soon(self._write_loop)
    ```
  - If a nursery cannot span the full lifetime, track the `cancel_scope`:
    ```python
    self.cancel_scope = trio.CancelScope()
    with self.cancel_scope:
        await self._run()
    ```

---

### 4. Stream Lifecycle & Half-Close Semantics
A multiplexed stream has two independent halves (Read half and Write half):

| Condition | Meaning | Expected Behavior |
|---|---|---|
| `StreamEOF` | Remote peer closed their write half (sent `FIN`) | Local read returns `StreamEOF` or `b""`. Local side can STILL write responses! |
| `StreamReset` | Remote peer abruptly aborted stream (sent `RST`) | Both read and write immediately raise `StreamReset`. Discard unwritten buffers. |
| `StreamClosed` | Local stream was explicitly closed locally | Further writes raise `StreamClosed`. |

- **Verification:**
  - Ensure reading loops handle `StreamEOF` cleanly without treating normal EOF as an unexpected crash.
  - Ensure `close()` is called in a `finally` block or `async with` context.

---

### 5. Timeouts with `trio.fail_after` (Zero Polling Loops)
Never implement timeouts with sleep loops (`while time.time() < deadline: await trio.sleep(0.1)`).

- ✅ **Standard Pattern:**
  ```python
  try:
      with trio.fail_after(RESP_TIMEOUT):
          data = await read_exactly(stream, LENGTH)
  except trio.TooSlowError:
      logger.debug("Timed out waiting for response from %s", peer_id)
      await stream.reset()
      raise
  ```

---

### 6. Resource Manager (`rcmgr`) Accounting
When opening or accepting streams, resource limits must be acquired from the ResourceManager and freed upon stream teardown:

- **Checklist:**
  - When opening a stream: `scope = await rcmgr.open_stream(peer_id, protocol_id, direction)`
  - When stream closes or errors: ensure `scope.done()` or `scope.release()` is executed in a `finally:` block.
  - Ensure memory allocations during payload reception call `scope.reserve_memory(n)` before allocating large buffers.

---

## Concurrency Audit Checklist

When reviewing any `py-libp2p` PR or module, verify:

- [ ] **No `stream.read(n)` on fixed-size frames:** Replaced with `read_exactly(stream, n)`.
- [ ] **No swallowed `trio.Cancelled`:** All try/except blocks re-raise `trio.Cancelled`.
- [ ] **No orphan coroutines:** Every background task runs in a nursery or cancel scope.
- [ ] **No sleep-polling timeouts:** Handled via `trio.fail_after(timeout)` or `trio.move_on_after`.
- [ ] **Stream Reset on Error:** Streams that error midway send `stream.reset()` to notify the remote peer rather than hanging.
- [ ] **Safe `finally:` cleanup:** Sockets, streams, and rcmgr scopes are released in `finally` blocks.
