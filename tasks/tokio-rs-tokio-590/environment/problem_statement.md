## Description

The line-splitting codec currently has no upper bound on how much data it will buffer while waiting for a newline character. When a server uses this codec to read input from untrusted clients, a malicious client could send an arbitrarily long stream of bytes with no newlines, causing the server to allocate an unbounded amount of memory — a straightforward denial-of-service vector.

## Expected Behavior

- The codec should support an optional maximum line length, configured at construction time.
- When a line being read exceeds the configured limit without a newline, the codec should return an error to the caller.
- After signaling the error, the codec should automatically discard the remainder of the over-long line and resume decoding subsequent lines normally — callers should not need to reinitialize the codec.
- Lines that are within the limit, including empty lines and lines with carriage-return/newline endings, should continue to be decoded correctly.
- When data arrives incrementally and has not yet reached the limit, the codec should wait for more data rather than prematurely erroring.
- If end-of-stream is reached with remaining buffered data and no final newline, the codec should return whatever data remains as the final line.

## Why This Matters

Without a length limit, any codec deployed in a network-facing service is vulnerable to memory exhaustion attacks. Having a built-in, easy-to-configure limit makes it straightforward to build safe, production-grade line-oriented servers.
