## Description

When checking out a branch in Dolt, tables that have been explicitly marked as ignored can still be silently overwritten by the incoming branch's version of those tables. This means that locally managed data — data the user deliberately excluded from version control — can be lost during a routine branch switch without any warning.

There is currently no way to opt into protection against this. Users who rely on ignore patterns to manage local-only data have no safeguard when switching between branches that have different versions of an ignored table.

## Expected Behavior

- A new checkout flag should allow users to opt in to protection: when the flag is used, checkout should abort if any locally-present ignored table differs on the target branch, and the error message should name the specific tables that would be overwritten.
- A complementary flag should allow users to explicitly signal that overwriting ignored tables is acceptable (preserving the existing behavior, but making intent explicit).
- Using both flags together in the same invocation should be rejected as a configuration error.
- The protection should be precise: it should only block checkout when an ignored table actually differs between branches. If the table is identical, if it only exists on the target branch, or if it only exists locally, no error should occur.
- The force flag for discarding working-set changes should not override the ignored-table protection.
- Creating a brand-new branch from the current HEAD should never be blocked by this protection, because no overwrite can occur.
- These flags should work both via the command-line interface and via the SQL stored procedure interface.

## Why This Matters

Dolt's ignore system lets teams manage tables that hold environment-specific or runtime data outside of version control. Without this protection, a simple branch checkout can silently destroy that data, which is unexpected and potentially disruptive.
