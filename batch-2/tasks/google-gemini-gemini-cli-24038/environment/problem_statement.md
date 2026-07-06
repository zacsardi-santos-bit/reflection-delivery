## Description

The sandbox security system currently requires forbidden paths to be provided as a static list at the time the sandbox manager is constructed. This is problematic because some forbidden paths — such as paths derived from project configuration files — can only be known after reading the filesystem, which happens asynchronously and potentially much later than startup. Forcing early resolution adds unnecessary overhead and limits flexibility.

Additionally, the current conflict resolution behavior between allowed and forbidden paths is ambiguous: when a path appears in both lists, the sandbox managers would add it to the allow list and then also deny it. This ordering dependency is fragile. The correct behavior should be that forbidden paths always win — a conflicting path should never appear in the allow list at all.

Finally, path comparison does not account for case-insensitive filesystems on macOS and Windows, which can allow paths that differ only in case to slip through conflict checks.

## Expected Behavior

- Forbidden paths should be supplied as a deferred function (resolved lazily only when needed), not as a static list provided at construction time.
- The configuration system should compute its forbidden paths lazily and only when the sandbox actually prepares a command — not at startup or during initialization.
- When a path appears in both the forbidden and allowed lists, it must be removed from the allowed list entirely, so it is only ever enforced as forbidden.
- Path deduplication and conflict detection must be case-insensitive on macOS and Windows, and case-sensitive on Linux.

## Why This Matters

This change ensures sandbox security rules are evaluated at the right time, avoids startup overhead from resolving paths that may not be needed, eliminates ambiguous allow-then-deny ordering, and correctly handles case-insensitive filesystems across platforms.
