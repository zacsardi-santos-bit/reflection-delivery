## Description

The memory search system currently supports two matching modes: match any query, or match all queries on the same line. A common use case is searching for multiple terms that appear *near* each other across consecutive lines, but the system has no way to express this — callers are forced to choose between "anywhere in the file" (too broad) or "all on the same line" (too strict).

This feature request adds a third matching mode: all queries must appear within a sliding window of N consecutive lines. This allows finding sections of a memory file where multiple concepts appear in proximity, even if not on the exact same line.

## Expected Behavior

- A new "all within N lines" matching mode lets callers specify a window size; a match is reported when all queries appear within any consecutive block of that many lines
- When a larger window would be reported that is entirely contained within a smaller valid window, the larger one is suppressed — only the tightest matching windows are returned
- Specifying a window size of zero is invalid and must be rejected immediately with a clear error
- The existing "all on same line" mode should be available under a clearer name that distinguishes it from the new windowed variant
- Server-level argument parsing must support the new mode, and any validation errors from an invalid window size must surface as invalid-parameter errors to callers

## Why This Matters

Without proximity-based search, users either get too many irrelevant results (any-match) or miss co-occurring concepts that span a few lines (all-on-same-line). The new mode makes it practical to find related information that appears close together in memory files.
