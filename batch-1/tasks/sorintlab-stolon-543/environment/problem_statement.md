## Description

Currently, configuring a standby cluster only supports specifying a direct streaming replication connection to the primary. There is no way to configure a standby cluster that stays synchronized via WAL archiving after initial restore. The cluster specification's standby settings are a flat structure that only holds streaming connection info, making it impossible to also provide archive recovery configuration for the ongoing replication phase.

## Expected Behavior

- The cluster specification should use a richer standby configuration container that can hold both streaming replication settings and archive recovery settings.
- It should be possible to configure a standby cluster that replays WAL segments from an archive (rather than requiring a live streaming connection) to stay in sync with the primary.
- A standby cluster using archive-based WAL recovery should be promotable to an independent primary cluster, with the database role transitioning to master after promotion.
- Existing standby clusters that use streaming replication should continue to work by nesting the existing connection settings inside the new configuration container.

## Why This Matters

Operators running geographically distributed or DR configurations often rely on WAL archiving rather than direct streaming connections between clusters. Without support for archive-based recovery in the ongoing standby phase (not just initial restore), such setups are not possible. This change makes standby clusters more flexible and production-ready for archive-driven replication scenarios.
