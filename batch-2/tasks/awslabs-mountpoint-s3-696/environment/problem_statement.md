## Description

The filesystem tool currently has two related issues around metadata TTL configuration and caching behavior:

1. **No upper bound on metadata TTL**: Users can specify arbitrarily large values for the metadata cache duration, including values that are so large they would overflow the integer type used to represent them. When this happens, the error message is confusing and not actionable. The tool should enforce a sensible maximum limit (approximately 100 years) and clearly explain why a given value was rejected.

2. **No negative caching**: When the filesystem performs a lookup for a file that doesn't exist in cloud storage, it currently re-queries the cloud on every subsequent lookup for the same path. This is inefficient — especially during operations where a missing file is repeatedly probed. A negative cache would allow the filesystem to remember that a file was not found and skip redundant cloud requests for the same missing path.

## Expected Behavior

- When a user provides a metadata TTL value larger than the allowed maximum, the tool should exit with a clear error message indicating the maximum allowed value (~100 years, expressed in seconds).
- When a user provides a metadata TTL value that cannot even be parsed as a valid integer (because it is too large to fit in the integer type), the tool should exit with a clear error message stating the number is too large.
- When the negative cache is enabled, a failed file lookup should be remembered, and subsequent lookups for the same path should not trigger additional cloud requests.
- A directory listing operation should be able to discover newly created files and invalidate any negative cache entries for those files, making them accessible via subsequent lookups.

## Why This Matters

Users running the filesystem with caching enabled need predictable, safe TTL values and clear feedback when they provide invalid input. The negative cache is important for workloads that probe for files that may not yet exist, preventing unnecessary cloud API calls and improving performance.
