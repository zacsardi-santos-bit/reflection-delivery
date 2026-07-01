## Description

CloudNativePG currently manages replication slots that it creates automatically for high-availability purposes, but it does not track or replicate user-created physical replication slots. If a user creates a physical replication slot directly on the primary, that slot exists only on the primary and is invisible to the operator. When a failover occurs, the new primary loses that slot, breaking any streaming replication clients that depended on it.

We need the operator to automatically synchronize user-created physical replication slots from the primary to all standby instances, just as it already does for its own HA-managed slots.

## Expected Behavior

- Any user-created physical replication slot on the primary should be automatically propagated to each standby.
- Users should be able to exclude specific slots from synchronization by configuring a list of regular expression patterns. Slots whose names match any of these patterns are not synchronized.
- The cluster admission webhook should validate that any configured exclusion patterns are valid regular expressions, rejecting the configuration with a clear error message if any pattern is malformed.
- The synchronization feature should be independently enable/disable-able from the HA replication slots feature.
- When the synchronization feature is disabled, the operator must remove previously synchronized copies from standbys, but must leave user-created slots intact on the primary.
- The operator must be able to distinguish between slots it manages for HA purposes and user-created slots so it can apply the correct lifecycle rules to each category.

## Why This Matters

Users rely on physical replication slots to keep streaming consumers connected after a failover. Without synchronization, those slots disappear on new primaries and break consumers. Automating this synchronization reduces manual operational burden and makes the cluster resilient to topology changes for all physical replication slots, not just operator-managed ones.
