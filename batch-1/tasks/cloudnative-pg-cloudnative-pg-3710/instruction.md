Implement support for synchronizing user-created physical replication slots from the primary to standby instances in the CloudNativePG operator. Ensure that the operator can distinguish between HA-managed slots and user-created slots, applying different lifecycle rules to each. Provide a mechanism to exclude specific slots from synchronization using regular expression patterns.

*   Update `SynchronizeReplicasConfiguration` struct in `api/v1/cluster_types.go`:
    *   Add `Enabled *bool` and `ExcludePatterns []string` fields.
    *   Include an unexported `compiledPatterns` field for caching compiled regex objects.

*   Implement `compileRegex` method on `SynchronizeReplicasConfiguration`:
    *   Return `nil` if the receiver is `nil` or all patterns compile successfully.
    *   Return a non-empty error slice if any pattern fails to compile.
    *   Cache results to avoid recompilation.

*   Implement `GetEnabled` method on `SynchronizeReplicasConfiguration`:
    *   Return `true` if the receiver is `nil`, `Enabled` is `nil`, or `*Enabled` is `true`.
    *   Return `false` only if `*Enabled` is explicitly `false`.

*   Implement `IsExcludedByUser` method on `SynchronizeReplicasConfiguration`:
    *   Return `(false, nil)` if the receiver is `nil`.
    *   Compile `ExcludePatterns` lazily if `compiledPatterns` is empty.
    *   Return `(true, nil)` if any pattern matches `slotName`.
    *   Return `(false, error)` for invalid patterns with specific error messages.

*   Update `validateReplicationSlots` method on `Cluster`:
    *   Validate regex patterns in `SynchronizeReplicasConfiguration`.
    *   Return a validation error if any pattern is invalid.

*   Modify `ReplicationSlot` struct in `internal/management/controller/slots/infrastructure/replicationslot.go`:
    *   Add `IsHA bool` field to distinguish HA slots by name prefix.

*   Update `dropReplicationSlots` function in `internal/management/controller/slots/reconciler/replicationslot.go`:
    *   Add `isPrimary` parameter to control behavior for primary vs. replica.
    *   Skip non-HA slots on primary and handle HA slots based on activity status.

*   Rename functions in `tests/utils/replication_slots.go`:
    *   `GetExpectedReplicationSlotsOnPod` to `GetExpectedHAReplicationSlotsOnPod`.
    *   `ToggleReplicationSlots` to `ToggleHAReplicationSlots`.

*   Add `ToggleSynchronizeReplicationSlots` function in `tests/utils/replication_slots.go`:
    *   Enable or disable the SynchronizeReplicas feature on a cluster.

*   Rename `AssertClusterReplicationSlots` to `AssertClusterHAReplicationSlots` in `tests/e2e/asserts_test.go`.

*   Update `AssertReplicationSlotsOnPod` function signature:
    *   Accept `expectedSlots []string`, `isActiveOnPrimary bool`, and `isActiveOnReplica bool`.
    *   Use `ContainElements` for slot verification.

*   Ensure synchronization of user-created slots on replicas when the feature is enabled, and clean up slots on replicas when disabled, preserving user-created slots on the primary.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.