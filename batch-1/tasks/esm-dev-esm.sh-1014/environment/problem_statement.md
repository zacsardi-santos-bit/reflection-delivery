## Description

The server currently stores build metadata for cached module builds using JSON encoding. This approach is verbose and relatively slow to parse at scale. We need a more compact, custom encoding format for build metadata that can be efficiently stored in and retrieved from a key-value database.

## Expected Behavior

- Build metadata — including whether a module is CommonJS-compatible, has associated CSS, exports a default value, is types-only, has an associated TypeScript definitions path, and lists its imports — should be encodable to a compact byte representation.
- Encoding a populated metadata record should produce a non-empty byte sequence.
- Decoding a previously encoded byte sequence should faithfully restore all original field values without loss or corruption.
- Encoding and then decoding an empty (zero-value) metadata record should produce a record with all boolean fields set to false, an empty definitions path, and no imports.

## Why This Matters

As the CDN serves a growing number of module builds, the overhead of JSON parsing for every metadata lookup adds up. A compact, custom binary format for build metadata reduces storage and parsing overhead, making the system faster and more efficient. This also enables storing build metadata in a lightweight embedded key-value store rather than a general-purpose file-based storage system.
