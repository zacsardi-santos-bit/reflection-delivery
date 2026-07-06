## Description

The Arrow IPC format reader in ClickHouse is vulnerable to heap out-of-bounds reads when processing crafted input files. An attacker (or a corrupt data source) can construct a valid-looking Arrow IPC file that declares a large row count in the metadata while providing only a tiny data buffer. When ClickHouse reads such a file, it trusts the declared row count and performs bulk memory copies or per-element reads beyond the end of the actual buffer, resulting in heap buffer overflows that are caught by sanitizers and could be exploited in production.

The problem affects nearly every code path in the Arrow column reader: fixed-width numeric types, dates, timestamps, intervals, booleans, decimals, dictionary-encoded columns, fixed-size binary types (used for UUIDs, IPv4, fixed strings, 128-bit and 256-bit integers), variable-length strings and binary, JSON columns, geo types, IPv6, view-based string types, and all nested types (lists, large lists, fixed-size lists, maps, structs).

## Expected Behavior

- Any Arrow IPC file where the declared row count is inconsistent with the actual buffer size must be rejected with a data integrity error before any unsafe memory access occurs.
- Arithmetic overflow in buffer-size calculations (e.g., a row count near 2^62 causing size × count to wrap around) must be detected and rejected.
- Validity bitmaps too small for the declared row count must be validated before any null-check operation.
- Invalid nested structures — non-monotonic or out-of-bounds list offsets, child arrays shorter than the parent requires, struct fields shorter than their parent, sliced structs with zero-length child fields, negative declared lengths — must all be rejected.
- Validation must happen before any memory reservation, so malformed lengths produce a data error rather than a memory allocation failure.

## Why This Matters

These vulnerabilities allow any user or external data source that can supply an Arrow IPC file to cause ClickHouse to perform heap out-of-bounds reads. Fixing them ensures that the reader fails safely with a consistent, actionable error whenever the file's metadata does not match its data buffers.
