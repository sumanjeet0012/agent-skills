---
name: pylibp2p-protocol-scaffolder
description: >
  End-to-end guide and code scaffolder for implementing, registering, and testing new libp2p protocols
  in py-libp2p. Covers multistream-select protocol IDs, protobuf message framing, stream handler patterns,
  outbound stream creation, and multi-host integration tests using Trio fixtures.
  Use when the user asks to "implement a new protocol in py-libp2p", "scaffold a libp2p protocol",
  "add a stream handler", "how to write a custom protocol in py-libp2p", or "write tests for a libp2p protocol".
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment"
license: MIT
---

# py-libp2p Protocol Scaffolder & Implementation Guide

A production blueprint for implementing custom peer-to-peer protocols in `py-libp2p`. Explains how to define protocol IDs, structure protobuf messages, register server stream handlers on a `Host`, dial outbound streams, and write automated integration tests.

---

## Trigger Phrases

| User Input | Deliverable |
|---|---|
| "Implement a new protocol in py-libp2p" | Full protocol template: ID, handler, client, and tests |
| "How to register a stream handler in py-libp2p" | `host.set_stream_handler` registration and stream reading pattern |
| "Write an integration test for py-libp2p protocol" | Trio `@pytest.mark.trio` multi-host swarm test |
| "Scaffold request-response protocol" | Complete protobuf + varint message framing client/server |

---

## 🏗️ Protocol Architecture Blueprint

A complete protocol implementation in `py-libp2p` consists of 5 core pieces:

```
1. Protocol Constant (TProtocol) ──► 2. Message Framing (Varint/Protobuf)
                                                │
5. Trio Integration Test ◄── 4. Client (Dial) ◄─┴─► 3. Server (Stream Handler)
```

---

## 1. Define Protocol ID & Constants

Protocol IDs follow the multistream-select specification: `/<protocol-name>/<semver>`.

```python
# libp2p/<my_service>/constants.py
from libp2p.custom_types import TProtocol

PROTOCOL_ID = TProtocol("/echo-service/1.0.0")
SERVICE_NAME = "libp2p.echo"
MAX_MSG_SIZE = 1024 * 1024  # 1 MB maximum payload limit
```

---

## 2. Server Stream Handler

Implement the inbound stream handler. The handler runs when a remote peer negotiates `PROTOCOL_ID` via multistream-select:

```python
# libp2p/<my_service>/service.py
import logging
import trio

from libp2p.abc import INetStream
from libp2p.io.utils import read_exactly
from libp2p.network.stream.exceptions import StreamClosed, StreamEOF, StreamReset
from .constants import PROTOCOL_ID, MAX_MSG_SIZE

logger = logging.getLogger(__name__)

async def echo_stream_handler(stream: INetStream) -> None:
    """
    Handle inbound echo stream from remote peer.
    """
    try:
        # 1. Read message length (e.g. 4-byte big-endian header or varint)
        raw_len = await read_exactly(stream, 4)
        msg_len = int.from_bytes(raw_len, byteorder="big")
        
        if msg_len > MAX_MSG_SIZE:
            logger.warning("Rejecting oversized message (%d bytes)", msg_len)
            await stream.reset()
            return

        # 2. Read full payload using read_exactly (prevents short-read bugs!)
        payload = await read_exactly(stream, msg_len)
        logger.debug("Received echo payload (%d bytes)", msg_len)

        # 3. Write echo response back to the stream
        await stream.write(raw_len + payload)

        # 4. Gracefully close local write half (sends EOF / FIN to peer)
        await stream.close()

    except (StreamEOF, StreamClosed):
        logger.debug("Stream closed by remote peer")
    except StreamReset:
        logger.debug("Stream reset by remote peer")
    except Exception as e:
        logger.error("Error processing stream: %s", e)
        await stream.reset()
```

---

## 3. Registering Protocol on the Host

Register the handler on your `BasicHost` or `RoutedHost`:

```python
# Server-side setup
host.set_stream_handler(PROTOCOL_ID, echo_stream_handler)
```

---

## 4. Client / Requester Implementation

Dial the remote peer and open a dedicated multiplexed stream:

```python
from libp2p.abc import IHost
from libp2p.peer.id import ID as PeerID

class EchoClient:
    def __init__(self, host: IHost):
        self.host = host

    async def echo(self, peer_id: PeerID, data: bytes, timeout: float = 10.0) -> bytes:
        """
        Open stream to peer_id, send data, and read back echo response.
        """
        # Open outbound stream for PROTOCOL_ID
        stream = await self.host.new_stream(peer_id, [PROTOCOL_ID])
        
        try:
            with trio.fail_after(timeout):
                # Send 4-byte length prefix + data
                length_bytes = len(data).to_bytes(4, byteorder="big")
                await stream.write(length_bytes + data)
                await stream.close()  # Half-close write side

                # Read response
                resp_len_bytes = await read_exactly(stream, 4)
                resp_len = int.from_bytes(resp_len_bytes, byteorder="big")
                response = await read_exactly(stream, resp_len)
                return response

        except Exception:
            await stream.reset()
            raise
```

---

## 5. Automated Integration Test (Trio Swarm Fixture)

Write an integration test connecting two virtual hosts over a simulated network:

```python
# tests/core/test_echo_protocol.py
import pytest
import trio

from libp2p.custom_types import TProtocol
from libp2p.tools.factories import HostFactory

PROTOCOL_ID = TProtocol("/echo-service/1.0.0")

@pytest.mark.trio
async def test_echo_protocol_roundtrip():
    async with trio.open_nursery() as nursery:
        # 1. Instantiate two test hosts
        host_a = HostFactory()
        host_b = HostFactory()

        # 2. Register handler on Host B
        received_data = []

        async def handler(stream):
            data = await stream.read(1024)
            received_data.append(data)
            await stream.write(data)
            await stream.close()

        host_b.set_stream_handler(PROTOCOL_ID, handler)

        # 3. Connect Host A to Host B
        await host_a.connect(host_b.get_peer_info())

        # 4. Host A opens stream to Host B
        stream = await host_a.new_stream(host_b.get_id(), [PROTOCOL_ID])
        test_msg = b"Hello, py-libp2p!"
        await stream.write(test_msg)
        await stream.close()

        # 5. Read response
        response = await stream.read(1024)
        assert response == test_msg
        assert received_data == [test_msg]

        # Cleanup
        await host_a.close()
        await host_b.close()
```

---

## Protocol Development Checklist

- [ ] **Protocol ID format:** Standardized as `/name/semver`.
- [ ] **Frame boundaries:** Uses explicit length prefixing (varint or big-endian integer).
- [ ] **Exact reads:** Uses `read_exactly(stream, length)` for all fixed-length reading.
- [ ] **Stream teardown:** Handles `StreamEOF`, calls `stream.close()` on success, `stream.reset()` on failure.
- [ ] **Timeout protection:** Wrapped in `with trio.fail_after(timeout):`.
- [ ] **Trio test:** Verified with `@pytest.mark.trio` across two connected test hosts.
