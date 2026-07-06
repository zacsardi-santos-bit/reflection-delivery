## Description

The codebase has several subsystems (including package registry handlers for different formats) that each directly import a third-party zstd compression library. There is no internal, centralized wrapper for zstd functionality. This makes it harder to maintain consistent behavior, add extended capabilities like seekable streams, or swap implementations in the future.

We need a new internal module that wraps zstd compression for use across the project. It should provide:

- A standard streaming compress/decompress API (writer and reader with optional configuration)
- A **seekable** compress/decompress API that encodes data in indexed frames, allowing efficient random access to arbitrary offsets without decompressing the entire stream
- The seekable format must be backward-compatible with the standard reader
- When creating a seekable reader, it should reject data that was not written in the seekable format

## Expected Behavior

- Standard writer/reader work correctly for round-trip compression of data
- Compression options (e.g., compression level, low-memory decode mode) can be configured via functional options
- Seekable writer divides uncompressed data into fixed-size blocks so an index can be built
- Seekable reader can seek to a byte offset in the uncompressed data and read from that position, reading only the relevant block(s) rather than the entire file
- Seeking to a position should involve only a small, bounded number of underlying I/O operations
- Closing a seekable reader also closes the underlying source if it supports closing
- Attempting to use the seekable reader on data produced by the standard writer returns an error

## Why This Matters

Having a centralized zstd module enables features like compressed action log storage with efficient partial viewing, while keeping the dependency management and extended capabilities in one place that other packages can import cleanly.
