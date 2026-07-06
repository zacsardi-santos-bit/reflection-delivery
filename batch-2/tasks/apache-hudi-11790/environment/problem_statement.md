## Description

When using ZooKeeper-based distributed locking with Hudi, users must currently configure explicit ZooKeeper-specific settings — the ZooKeeper path and lock key — in addition to all the other table configuration they've already set up. This is redundant and error-prone: the information needed to uniquely identify a table (its base path and name) is already present in the Hudi configuration, yet users must re-express it in a completely different form just to enable locking.

There is no way to use ZooKeeper locking "out of the box" based on table identity — you always need to know and specify ZooKeeper internals manually.

## Expected Behavior

- A new lock provider should be available that automatically derives the ZooKeeper coordination path from the Hudi table's own base path and name settings, requiring no explicit ZooKeeper path configuration from the user.
- When a configuration contains the required table base path and table name, this new provider should initialize successfully and allow lock acquisition and release.
- When a configuration is missing the required table identity information, the new provider should fail at initialization with a clear validation error — not silently or at lock acquisition time.
- The existing explicit-path lock provider should continue to work for users who prefer direct control over the ZooKeeper path and key, and it should similarly fail at initialization with a clear validation error when its required configuration is absent.
- Both providers should share a common base type so that code working with lock providers generically can use either one interchangeably.

## Why This Matters

Users already provide their table base path and name as part of standard Hudi configuration. Having to translate that into a ZooKeeper path manually creates unnecessary friction, increases the chance of misconfiguration, and couples the ZooKeeper setup to table-specific knowledge that the system should be able to handle automatically.
