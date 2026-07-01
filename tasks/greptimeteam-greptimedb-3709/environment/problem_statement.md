## Description

There are two related issues in the storage engine's region management that should be cleaned up together:

1. **Misleading error code for non-ready regions**: When a write is attempted on a region that is not currently accepting writes — whether because it is a follower region opened in read-only mode or because it has been gracefully set to read-only — the returned error incorrectly signals that the region is "read-only." A more accurate error would indicate that the region is simply not ready to accept writes. This distinction matters for callers trying to understand why a write failed and whether retrying makes sense.

2. **Overly complex manifest access on regions**: Accessing a region's current manifest requires callers to acquire a read lock on the manifest manager, then invoke the manifest accessor while holding that lock. This is verbose and error-prone. A cleaner design would expose the manifest through a dedicated context object on the region that provides a simple async interface without explicit lock management.

3. **Unnecessary error handling on manifest manager shutdown**: The manifest manager's shutdown method currently returns a result that callers must handle, but in practice this operation cannot fail in a meaningful way. Making it infallible removes unnecessary boilerplate from all shutdown call sites.

## Expected Behavior

- Writes to non-writable regions return a "not ready" error instead of a "read-only" error.
- Region objects provide a manifest context field with a simple async method to retrieve the current manifest.
- The manifest manager shutdown method returns nothing (is infallible) and does not require callers to unwrap a result.

## Why This Matters

Accurate error codes help callers understand the cause of failures and whether recovery is possible. Simplified interfaces reduce cognitive overhead and prevent subtle lock-management bugs.
