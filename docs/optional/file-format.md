# File Format Reference

<!-- OPTIONAL DOC: Include this file only if your project reads or writes custom binary or
     structured file formats (e.g. database page files, save files, archive formats, bytecode,
     WAL/journal files, index files).
     To use: copy this file to docs/file-format.md and add it to the Related files headers
     in design.md, spec.md, tasks.md, and the Canonical Sources table in CLAUDE.md. -->

> **Related files:** [../design.md](../design.md) (system design) | [../adr/](../adr/) (why these choices) | [../../constitution.md](../../constitution.md) (immutable rules)
>
> If your project also has a wire protocol: | [protocol.md](protocol.md) (network layer)

This document is the single source of truth for all on-disk file formats — byte layouts, magic numbers, versioning, and invariants. **Read this before implementing any read/write path.**

> **This doc owns:** All file format layouts, field byte offsets, alignment rules, format versions, and corruption-detection schemes. Other docs reference format details here by link — they never repeat byte layouts inline.

---

## Files Overview

| File / Extension | Purpose | Format |
|---|---|---|
| <!-- TODO --> | | |

<!-- Example rows:
| `*.db`          | Main data file     | See [Main Data File](#main-data-file)       |
| `*.wal`         | Write-ahead log    | See [WAL File](#wal-file)                   |
| `*.idx`         | Index file         | See [Index File](#index-file)               |
-->

---

## Encoding Conventions

| Type | Encoding |
|---|---|
| Integers | <!-- TODO: big-endian / little-endian --> |
| Strings | <!-- TODO: length-prefixed UTF-8 / null-terminated / etc. --> |
| Booleans | <!-- TODO: single byte 0x00/0x01 --> |
| Alignment | <!-- TODO: fields aligned to N-byte boundaries / packed --> |

---

## [Main Data File]

<!-- TODO: rename this section to match your format, e.g. "Database File", "Save File", etc. -->

### Magic Number & Header

Every file of this type begins with a fixed-size file header:

```
Offset  Length  Field          Description
------  ------  -------------  -----------
0       4       magic          Magic bytes: TODO (e.g. 0x53514C69 for SQLite)
4       2       version        Format version (see Versioning)
6       2       flags          File-level flags (see Flags)
8       4       page_size      <!-- TODO: size of each page in bytes, if paged -->
12      4       page_count     <!-- TODO: total number of pages -->
16      N       <!-- TODO -->  <!-- TODO: remaining header fields -->
```

**Magic bytes:** `TODO` — reject any file that doesn't start with this exact sequence.

### Flags

| Bit | Name | Meaning |
|---|---|---|
| 0x0001 | `READ_ONLY` | File was closed cleanly; safe to read without recovery |
| 0x0002 | `DIRTY` | File was not closed cleanly; recovery required before use |
<!-- TODO: add format-specific flags -->

---

### Page / Block Layout

<!-- TODO: if your format is paged (like a B-tree or database), describe the page structure.
     If not paged, remove or rename this section. -->

Each page is `[page_size]` bytes. All pages share a common page header:

```
Offset  Length  Field       Description
------  ------  ----------  -----------
0       1       page_type   Type of page (see Page Types)
1       1       reserved    Must be 0x00
2       2       free_bytes  Bytes of free space on this page
4       4       checksum    CRC-32 of the remaining page bytes
8       N       body        Page-type-specific content
```

### Page Types

| Type ID | Name | Description |
|---|---|---|
| `0x01` | `ROOT` | B-tree root or file root page |
| `0x02` | `INTERIOR` | Internal B-tree node |
| `0x03` | `LEAF` | Leaf node — stores actual records |
| `0x04` | `OVERFLOW` | Continuation of a record too large for one page |
| `0x05` | `FREELIST` | Tracks free/reusable pages |
<!-- TODO: add format-specific page types -->

---

### Record Format

```
Offset  Length  Field       Description
------  ------  ----------  -----------
0       2       key_len     Length of key in bytes
2       4       value_len   Length of value in bytes
6       key_len key         Key bytes
6+key_len value_len value   Value bytes
```

<!-- TODO: adjust record layout to match your format -->

---

## [WAL / Journal File]

<!-- TODO: include if your format has a write-ahead log or journal.
     Remove this section if not applicable. -->

### Purpose

The WAL file records all pending writes before they are applied to the main data file. On crash recovery, the WAL is replayed to restore consistency.

### WAL Header

```
Offset  Length  Field        Description
------  ------  -----------  -----------
0       4       magic        WAL magic bytes: TODO
4       4       sequence     Monotonically increasing WAL generation number
8       4       checksum     CRC-32 of the header bytes (excluding this field)
```

### WAL Frame

Each WAL frame describes one page write:

```
Offset  Length  Field        Description
------  ------  -----------  -----------
0       4       page_number  The page being written
4       4       db_size      DB page count after this commit (0 = uncommitted)
8       4       checksum     CRC-32 of this frame + page data
12      N       page_data    Full page content (page_size bytes)
```

**Commit detection:** A frame is committed if `db_size > 0` and the checksum is valid. Frames with `db_size == 0` are uncommitted and must not be applied.

---

## Versioning

| Version | Changes |
|---|---|
| 1 | Initial format |
<!-- TODO: update as format evolves -->

**Rules:**
- A reader must reject files with a `version` higher than it understands.
- Backwards-incompatible changes require a new `version` value.
- New optional fields may be appended to headers only if readers skip unknown trailing bytes.

---

## Integrity & Corruption Detection

| Mechanism | Scope | Algorithm |
|---|---|---|
| Magic number check | File header | Exact byte match |
| Page checksum | Each page | <!-- TODO: CRC-32 / xxHash / etc. --> |
| WAL frame checksum | Each WAL frame | <!-- TODO --> |

**On corruption detected:** <!-- TODO: describe recovery or fail-fast behaviour -->

---

## Invariants

- The first page (page 0) is always the file header page.
- A file with `DIRTY` flag set must not be opened read-write without WAL recovery.
- Free pages must always appear in the freelist before being reused.
- <!-- TODO: add format-specific invariants -->
