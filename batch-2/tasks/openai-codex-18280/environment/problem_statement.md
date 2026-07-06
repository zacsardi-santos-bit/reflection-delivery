## Description

When the TUI loads a thread for metadata inspection or replay purposes, it incorrectly reuses the active primary session's permission configuration in the resulting session state. Because permission configurations can be tied to specific working directories, carrying them over to a read/replay session that has a different working directory causes those directory-bound permission rules to be misinterpreted against the wrong path.

## Expected Behavior

- Active, running sessions should have a properly populated permission profile derived from their sandbox policy and working directory.
- Sessions created from reading a historical or replayed thread (metadata/replay hydration) must start with no inherited permission profile — the field should be explicitly absent rather than copied from the primary session.
- The session state produced for a read thread must correctly reflect that thread's own identity and working directory, with no contamination from the primary session's permission configuration.

## Why This Matters

If a read thread's session state inherits the primary session's permission profile, any permission rules that were anchored to the primary session's working directory will be silently reapplied in the context of a different working directory. This can result in incorrect permission decisions for the replayed thread, since the path-relative permissions no longer match the actual file system context of the thread being read.
