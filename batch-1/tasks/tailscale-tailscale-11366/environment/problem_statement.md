## Description

The TailFS file sharing feature needs several improvements to its share management API. Currently, shares are stored and returned in an unordered format, so the list appears in a different order every time. There is also no built-in way to rename an existing share — users must delete and recreate it, which is cumbersome. Additionally, certain error conditions (such as "feature not enabled" or "invalid share name") are represented as opaque, private errors, making it impossible for callers to distinguish them programmatically.

## Expected Behavior

- The list of shares should always be maintained and returned in consistent alphabetical order.
- A rename operation should be available that atomically renames an existing share to a new name.
- Errors for "feature not enabled" and "invalid share name" should be exported (publicly accessible) so callers can detect and handle these conditions using standard error matching.
- Attempting to rename to a name that already exists should return a distinct, detectable error (already-exists).
- Attempting to rename a share that does not exist should return a distinct, detectable error (not-found).
- Share name normalization (trimming whitespace, enforcing lowercase) should be applied consistently to names provided to all share management operations.
- The internal storage format for shares should move from an unordered key-value map to a sorted list.
- The user preference system should store shares as part of the standard preference set, not in a separate state store.
- The "shares" field in the preference struct should be recognized by the system but should not expose a CLI flag through the general-purpose connection command — it belongs to the dedicated share management subcommand.

## Why This Matters

Users managing shared directories on their tailnet need consistent ordering and the ability to rename shares without losing the share's configuration. Developers integrating with the sharing API need to be able to detect specific failure conditions rather than treating all errors as generic failures.
