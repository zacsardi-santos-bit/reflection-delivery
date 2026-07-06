## Description

The minio-go client currently treats a Cloudflare-specific HTTP error code as a retryable server error, causing the client to unnecessarily retry requests when Cloudflare returns its catch-all response for unexpected origin server behavior.

## Expected Behavior

- Cloudflare-specific HTTP error codes that do not represent transient server-side issues should not be retried.
- Specifically, the Cloudflare catch-all error (HTTP 520) should be excluded from the list of retryable HTTP status codes, as retrying this type of error is unlikely to resolve the underlying problem and wastes resources.

## Why This Matters

When a client sits behind Cloudflare and the origin server returns something unexpected, Cloudflare uses this non-standard status code. Unlike standard gateway errors (bad gateway, service unavailable, gateway timeout), this code represents a Cloudflare-level interception rather than a transient server failure. Treating it as retryable leads to unnecessary retry cycles and increased load without any benefit.
