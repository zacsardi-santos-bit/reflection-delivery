## Description

Completed analysis runs currently accumulate in the cluster indefinitely. There is no built-in mechanism for the analysis controller to automatically clean up finished runs based on how long ago they completed or what their outcome was. Operators who want to keep their clusters tidy must either manually delete old analysis runs or rely on indirect rollout-level history limits, which don't cover all cases.

## Expected Behavior

- Users should be able to configure a time-to-live policy directly on an analysis run that specifies how long the run should be retained after it finishes.
- Separate retention periods should be configurable depending on whether the run succeeded, failed, or simply completed (regardless of outcome).
- Phase-specific retention settings (for success or failure) should take precedence over the general completion retention when both are set and the run's phase matches.
- A phase-specific retention setting should only apply to runs in its matching phase — a success retention should not cause deletion of failed runs, and vice versa.
- Once a run finishes, the controller should record its completion time and automatically delete the run during reconciliation once its configured TTL has elapsed.
- Runs that are still in progress, have no TTL policy, are already being deleted, or have no recorded completion time should never be garbage collected by this mechanism.

## Why This Matters

Without automatic cleanup, long-running systems accumulate large numbers of stale analysis run objects that consume etcd storage and clutter the namespace. Giving operators fine-grained TTL control — including per-outcome retention policies — lets them balance auditability with resource hygiene.
