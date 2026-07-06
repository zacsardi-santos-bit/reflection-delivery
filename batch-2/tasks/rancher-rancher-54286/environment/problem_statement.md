## Description

The handler responsible for syncing the system-upgrade-controller status condition on control plane objects has two problems:

1. **Fragile connectivity check**: Connectivity to the downstream cluster is currently determined by doing an indirect lookup through a separate cache of provisioning cluster objects and checking a connectivity condition flag. This lookup can fail if the cache is unavailable, causing the handler to return an error instead of simply skipping. The connectivity information is already available directly on the control plane status object and should be used instead.

2. **No rate-limiting on downstream API calls**: Every reconcile event triggers a new API call to the downstream cluster, regardless of how recently one was made. During periods of frequent reconciliation this can put unnecessary load on downstream clusters. The handler should rate-limit these calls to at most once every 30 seconds per control plane object.

## Expected Behavior

- Connectivity should be determined using the agent connection flag already present on the control plane status, eliminating the dependency on the provisioning cluster cache.
- Downstream API calls should be throttled to a maximum of once per 30-second window per control plane object. When the window is active, the handler should schedule a single delayed retry rather than stacking up repeated calls. If a retry is already scheduled, no additional retry should be queued.
- When the throttle window expires, the handler should proceed normally and record the time of the new call.
- On transient errors, the throttle state should be cleared so that retries are not artificially delayed.
- When a control plane object is deleted, its throttle state should be cleaned up.

## Why This Matters

Removing the cluster cache lookup simplifies the handler and prevents spurious errors when the cache is temporarily unavailable. The throttle reduces unnecessary load on downstream clusters in high-churn scenarios.
