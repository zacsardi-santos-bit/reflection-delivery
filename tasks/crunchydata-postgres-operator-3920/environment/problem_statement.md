## Description

When a PostgreSQL data volume in a managed cluster runs low on disk space, administrators currently have no automated recourse — they must manually detect the problem and resize the volume themselves. This creates operational burden and leaves production databases vulnerable to running out of space.

We need the operator to automatically monitor disk utilization in each PostgreSQL pod and expand the corresponding storage claim when usage gets high, up to a configurable maximum size. The feature should be gated so clusters opt into it explicitly, and the operator should emit appropriate events when expansions occur, when volumes reach their maximum size, or when the desired expansion would exceed the configured ceiling.

## Expected Behavior

- A disk-monitoring process running inside each database pod periodically checks how full the data volume is
- When usage exceeds a threshold (75%), the pod signals that a larger volume is needed, requesting approximately 1.5× the current size
- The operator detects this signal and reconciles the storage claim to the new desired size
- If the desired size exceeds a configured storage limit, the request is capped at the limit
- If the storage request in the cluster spec already exceeds the limit, the limit value is used and a warning event is emitted
- The operator emits events for volume expansions, limit-reached conditions, and cases where the desired size exceeds the limit
- Changes to the signal annotation on pods trigger a reconciliation of the cluster

## Why This Matters

Production PostgreSQL clusters can fill up unexpectedly. With automatic volume growth, the operator can proactively expand storage before the database becomes unavailable, reducing on-call burden and improving reliability.
