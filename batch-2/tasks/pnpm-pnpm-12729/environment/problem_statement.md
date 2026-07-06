## Description

The package registry proxy currently buffers entire upstream tarballs before sending any bytes to the client. This means every client waits for the proxy to download and fully verify the tarball before receiving anything — adding significant latency for large packages. We should stream bytes to the client as they arrive from upstream rather than waiting for the full download to complete first.

## Expected Behavior

- When the proxy fetches a tarball from upstream, it should begin forwarding bytes to the client immediately, without waiting for the full download to finish.
- The cache entry should still only be promoted after the full body has been received and its integrity verified — so nothing unverified ever lands in the cache.
- When an upstream serves bytes that fail integrity verification, those bytes should still be forwarded to the client (which can do its own verification), but the cached copy should be discarded. The response should return a 200 status; the SRI mismatch should not result in an error status code since the client will re-verify anyway.
- When the upstream stream errors or is truncated mid-transfer, the response should already have a 200 status (since the status line was sent early), and the error should surface as a body-level failure. The incomplete bytes should never reach the cache.
- If a client disconnects mid-download (drops the stream), the temporary cache file should be cleaned up.
- If the upstream declares an oversized body (via Content-Length), the request should be rejected before streaming begins, and the temporary file should be removed immediately.

## Why This Matters

Large tarballs cause noticeable delays when the proxy must buffer everything before responding. Streaming improves time-to-first-byte while preserving the security guarantee that only integrity-verified content is cached for future requests.
