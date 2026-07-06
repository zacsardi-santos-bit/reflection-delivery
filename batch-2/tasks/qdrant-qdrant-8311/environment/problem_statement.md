## Description

Qdrant currently has no way for internal components or authorized clients to inspect or read raw files within a collection's on-disk storage through the gRPC interface. There is no mechanism to check whether a specific file exists, measure its size, or retrieve its byte contents — whether as a single range, a batch of ranges, or an entire file at once. A streaming variant for large files is also missing.

## Expected Behavior

A new gRPC-only service should be added that exposes the following operations on per-collection storage files:

- **Check existence** — determine whether a file at a given path exists within a collection's storage directory, returning a boolean result.
- **List files** — list all files under a given path prefix within a collection's storage directory, returning relative paths.
- **File length** — return the byte size of a specific file.
- **Read byte range** — read a contiguous slice of bytes from a file given an offset and length.
- **Stream byte range** — like the above, but for large reads: deliver data in fixed-size chunks over a streaming response.
- **Read whole file** — return the complete contents of a file in a single response.
- **Read batch** — read multiple non-contiguous byte ranges from a single file, returning each slice in order.
- **Read multi** — read byte ranges from multiple files at once, returning each slice in request order.

The service must enforce strict path safety: any path that attempts to escape the collection's storage directory — through parent-directory references, special relative-path prefixes, or filesystem symlinks — must be rejected with an appropriate error. Empty path entries must also be rejected.

The service must integrate with the existing collection-level access control model so that callers can only access files belonging to collections they are authorized to read.

## Why This Matters

This capability is needed for internal operations such as shard transfers or remote storage inspection, where one node needs to read specific files from another node's collection storage without going through higher-level collection APIs.
