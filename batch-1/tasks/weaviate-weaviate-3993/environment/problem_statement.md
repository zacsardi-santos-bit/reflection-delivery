## Description

The distributed schema store component needs two related improvements:

1. **Interface renaming for clarity**: The interface that the store uses to communicate with the underlying persistence layer currently has a generic name that doesn't distinguish it clearly from the metadata-tracking layer. Since this interface handles physical index operations (reading and writing actual collection data to disk), it should be named to reflect that role rather than using a generic "database" label. All existing callers need to be updated accordingly.

2. **Test coverage for the command processor**: The Raft log command processor — the component that handles incoming replicated operations like adding collections, updating tenants, managing shard status, and deleting classes — has no automated test coverage. This makes it hard to verify that error conditions are handled correctly and consistently.

## Expected Behavior

- The interface for index-level operations should have a name that clearly conveys its role as an indexer, not just a generic database handle.
- The command processor should always return a structured response type, even for non-schema log entries.
- Malformed or invalid commands should produce errors that are identifiable as bad-request failures (using standard Go error wrapping and sentinel values).
- Operations that violate schema invariants (adding a class that already exists, referencing a class or tenant that doesn't exist) should produce errors identifiable as schema-conflict failures.
- Encountering an unknown command type should cause a panic to signal that an application upgrade is needed.
- Successful operations should be reflected in the in-memory schema state immediately after the command is applied.

## Why This Matters

Without test coverage on the command processor, regressions in error handling go undetected. The interface rename makes the codebase easier to understand by reducing ambiguity about which layer each component talks to. Together, these changes improve correctness guarantees and code clarity in a part of the system that is critical for distributed consistency.
