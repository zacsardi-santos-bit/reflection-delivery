Implement a richer standby configuration for clusters that supports both streaming replication and archive recovery settings. Update the cluster specification to allow standby clusters to synchronize using WAL archiving and enable promotion to a primary cluster.

*   Replace the `StandbySettings` field in the `ClusterSpec` struct with a new `StandbyConfig` field.
    *   Define `StandbyConfig` in `internal/cluster/cluster.go` with two optional pointer fields:
        *   `StandbySettings *StandbySettings` (JSON key 'standbySettings')
        *   `ArchiveRecoverySettings *ArchiveRecoverySettings` (JSON key 'archiveRecoverySettings')
*   Ensure `ClusterSpec` validation requires `StandbyConfig` to be non-nil when the cluster role is 'standby'.
    *   Return the error message 'standbyConfig undefined. Required when cluster role is "standby"' if `StandbyConfig` is absent.
*   Allow configuration of a standby cluster with `StandbyConfig` containing only `ArchiveRecoverySettings`.
    *   Enable synchronization of replicated data from a primary via WAL archiving without a direct streaming connection.
    *   Ensure the standby cluster can be promoted to a primary cluster using the cluster promotion command, transitioning the database role to master.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.