## Description

When upgrading rqlite from an older release to a newer version, nodes that had previously taken snapshots are unable to start correctly with their existing data. The new release uses a different, generation-based snapshot storage format that is incompatible with the older snapshot layout. As a result, users who upgrade lose access to their previously snapshotted data, which defeats the purpose of having snapshots in the first place.

## Expected Behavior

- When a node is started with a data directory containing snapshots created by an older release, it should automatically migrate those snapshots into the current storage format without any manual steps.
- After migration, the node should be able to read and serve data from the migrated snapshots — queries against the database should return the correct results.
- If no old-format snapshots are present (either the old snapshot directory does not exist, or it is empty), the migration step should succeed silently with no error.
- After a successful migration, the old snapshot directory should be removed to prevent double-migration.

## Why This Matters

Users upgrading between major rqlite releases need confidence that their data is preserved. Without this migration capability, any deployment that has been running long enough to produce snapshots would lose its data history when upgrading, making in-place upgrades impractical for production systems.
