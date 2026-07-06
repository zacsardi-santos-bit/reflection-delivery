## Description

When operating a PostgreSQL replication topology with multiple clusters, there is currently no safe, verifiable mechanism to promote a replica cluster to primary. Operators can request a promotion, but there is nothing preventing the promotion from happening prematurely — before the replica has actually caught up to the required state — which can cause data loss or split-brain situations.

We need a token-based promotion system to make cluster switchovers safe and verifiable. When a cluster is demoted from primary to replica, it should generate a "demotion token" that encodes its last known checkpoint state (timeline, write-ahead log position, and system identity). The replica cluster being promoted must present this token, and the system should verify that the replica has actually reached the required state before allowing the promotion to proceed.

## Expected Behavior

- When a cluster is demoted, it records a token in its status capturing its last checkpoint state.
- A replica cluster's spec accepts a promotion token field to request a promotion.
- Webhook validation rejects promotion tokens that are not properly encoded or are missing required fields.
- A method on the cluster type determines whether a promotion is needed by comparing the current promotion token against the last successfully applied one, avoiding duplicate processing.
- The system can distinguish between retryable failures (replica hasn't caught up yet) and permanent failures (the required state can no longer be matched or system identities differ).
- The cluster status also tracks the last successfully applied promotion token to prevent re-processing.

## Why This Matters

Without this mechanism, promoting a replica cluster is an informal, unverifiable operation that can silently corrupt data or create an inconsistent topology. With token-based promotion, operators get a safe, declarative way to perform controlled switchovers with clear feedback about whether the failure is transient or permanent.
