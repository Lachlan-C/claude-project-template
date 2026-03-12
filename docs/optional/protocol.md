# Wire Protocol Reference

<!-- OPTIONAL DOC: Include this file only if your project implements a binary or text-based
     network protocol (e.g. TCP wire protocol, custom framing, Redis/MongoDB/HTTP-compatible
     protocol, BitTorrent, P2P, RPC).
     To use: copy this file to docs/protocol.md and add it to the Related files headers
     in design.md, spec.md, tasks.md, and the Canonical Sources table in CLAUDE.md. -->

> **Related files:** [../design.md](../design.md) (system design) | [../adr/](../adr/) (why these choices) | [../../constitution.md](../../constitution.md) (immutable rules)
>
> If your project also has a file format: | [file-format.md](file-format.md) (on-disk layout)

This document is the single source of truth for the network protocol — framing, message types, byte layout, and connection lifecycle. **Read this before implementing any send/receive logic.**

> **This doc owns:** All message formats, field byte offsets, encoding rules, connection state machine, and compatibility constraints. Other docs reference protocol details here by link — they never repeat frame layouts inline.

---

## Overview

<!-- TODO: describe the protocol in one paragraph — transport (TCP/UDP/custom), framing style,
     client/server or peer-to-peer, text or binary, stateful or stateless -->

| Property | Value |
|---|---|
| Transport | <!-- TODO: TCP / UDP / Unix socket / etc. --> |
| Encoding | <!-- TODO: binary big-endian / little-endian / text / mixed --> |
| Framing | <!-- TODO: length-prefixed / delimiter / fixed-size / etc. --> |
| Default port | <!-- TODO --> |
| Versioned | <!-- TODO: yes/no — how versions are negotiated --> |

---

## Connection Lifecycle

```
Client                          Server
  |                               |
  |--- [Handshake / Hello] ------>|
  |<-- [Handshake / OK / Error] --|
  |                               |
  |--- [Request] ---------------->|
  |<-- [Response] ----------------|
  |                               |
  |--- [Close / Goodbye] -------->|
  |<-- [Close ACK] ---------------|
```

### States

| State | Description | Valid transitions |
|---|---|---|
| `CONNECTING` | TCP established, awaiting handshake | → `OPEN`, `ERROR` |
| `OPEN` | Ready to exchange messages | → `CLOSING`, `ERROR` |
| `CLOSING` | Graceful teardown in progress | → `CLOSED` |
| `CLOSED` | Connection terminated | — |
| `ERROR` | Fatal protocol error — connection must close | → `CLOSED` |

---

## Frame / Message Format

### Common Header

Every message begins with this fixed-size header:

```
Offset  Length  Field        Description
------  ------  -----------  -----------
0       1       version      Protocol version (currently: TODO)
1       1       type         Message type (see Message Types table)
2       2       flags        Bitmask of option flags
4       4       length       Payload length in bytes (uint32, big-endian)
8       N       payload      Message-specific payload
```

<!-- TODO: adjust header layout to match your protocol -->

### Flags

| Bit | Name | Meaning |
|---|---|---|
| 0x01 | `COMPRESSED` | Payload is compressed (TODO: algorithm) |
| 0x02 | `ERROR` | Response indicates an error |
| <!-- TODO --> | | |

---

## Message Types

| Type ID | Name | Direction | Description |
|---|---|---|---|
| `0x01` | `HANDSHAKE` | Client → Server | Open connection, declare version |
| `0x02` | `HANDSHAKE_OK` | Server → Client | Handshake accepted |
| `0x03` | `HANDSHAKE_ERR` | Server → Client | Handshake rejected |
| `0x10` | `REQUEST` | Client → Server | <!-- TODO --> |
| `0x11` | `RESPONSE` | Server → Client | <!-- TODO --> |
| `0xFF` | `GOODBYE` | Both | Graceful close |
<!-- TODO: fill in all message types -->

---

## Message Definitions

### `HANDSHAKE` (0x01)

**Direction:** Client → Server

```
Offset  Length  Field            Description
------  ------  ---------------  -----------
0       2       client_version   Protocol version the client supports
2       2       reserved         Must be 0x0000
```

**Behaviour:**
- Client must send within TODO ms of TCP connection or server closes.
- If `client_version` is unsupported, server replies `HANDSHAKE_ERR`.

---

### `REQUEST` (0x10)

**Direction:** Client → Server

```
Offset  Length  Field       Description
------  ------  ----------  -----------
0       4       request_id  Unique ID for correlating responses (uint32)
4       N       body        <!-- TODO: encoding of command/query body -->
```

---

### `RESPONSE` (0x11)

**Direction:** Server → Client

```
Offset  Length  Field       Description
------  ------  ----------  -----------
0       4       request_id  Mirrors the request_id from the REQUEST
4       N       body        <!-- TODO: encoding of result/error body -->
```

---

## Error Codes

| Code | Name | Description |
|---|---|---|
| `0x00` | `OK` | Success (used in response body, not as error) |
| `0x01` | `UNKNOWN_VERSION` | Client version not supported |
| `0x02` | `MALFORMED_FRAME` | Header or payload failed to parse |
| `0x03` | `INTERNAL_ERROR` | Server-side failure |
<!-- TODO: add domain-specific error codes -->

---

## Encoding Conventions

| Type | Encoding |
|---|---|
| Integers | <!-- TODO: big-endian / little-endian --> |
| Strings | <!-- TODO: length-prefixed UTF-8 / null-terminated / etc. --> |
| Booleans | <!-- TODO: single byte 0x00/0x01 --> |
| Arrays | <!-- TODO: uint32 count followed by elements --> |

---

## Compatibility Rules

- **Never** change the meaning or layout of an existing message type.
- **Never** reuse a retired type ID.
- New optional fields may be appended to a payload only if the receiver ignores unknown trailing bytes.
- Breaking changes require a new `version` value and a negotiation step.
