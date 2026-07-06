## Description

There are three independent security and correctness issues that need to be fixed:

### 1. Sensitive headers forwarded on cross-origin redirects

When the HTTP client automatically follows a redirect, it currently forwards all original request headers — including authentication credentials — to the redirect target, even when that target is a completely different server or port. A malicious server could exploit this by issuing redirects to a different host and harvesting credentials (auth tokens in headers, cookies, or URL-embedded usernames/passwords). The client should detect when a redirect crosses an origin boundary and strip sensitive auth-related headers before following it. Same-origin redirects should continue to pass these headers through unchanged.

### 2. Gzip decompression ignores the body size limit

When the server is configured to automatically decompress incoming request bodies and enforce a maximum body size, the size check is applied only to the compressed data. This allows a "decompression bomb": a tiny compressed payload that expands to a body far larger than the configured limit, effectively bypassing the guard. The size limit must also be enforced on the decompressed output, and requests that exceed it should be rejected with an appropriate log message.

### 3. WebSocket masking function does not validate key length

The WebSocket protocol mandates that masking keys are exactly 4 bytes long. The masking utility functions (both the pure-Python implementation and the compiled C extension) currently accept keys of any length without complaint. Passing a key that is not 4 bytes can produce silent data corruption or undefined behavior. These functions should immediately raise a descriptive error when given a key of the wrong length.

## Expected Behavior

- Cross-origin HTTP redirects should strip auth credentials from the forwarded request; same-origin redirects should preserve them
- Decompressed request bodies must be measured against the configured size limit; oversized decompressed bodies must be rejected and logged
- Both the Python and C WebSocket mask implementations must raise an error for any mask key that is not exactly 4 bytes long

## Why This Matters

These are security-relevant correctness issues. Credential leakage via open redirects is a known attack vector, and decompression bombs are a well-documented denial-of-service technique. The masking validation prevents incorrect protocol behavior that could silently corrupt data.
