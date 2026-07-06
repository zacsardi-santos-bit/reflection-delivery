## Description

The TiDB component in a TiDB Operator-managed cluster currently has no way to control how many new TiDB instances can come online simultaneously during a scale-out operation. Every scale-out adds exactly one new pod per reconciliation loop, regardless of how quickly the user needs to scale. Other components (such as TiFlash and TiKV) already support a parallelism setting that controls how many pods can be scaled out at the same time. The TiDB component should support the same feature.

## Expected Behavior

- The TiDB component spec should accept a scale policy configuration, including a "scale-out parallelism" setting that controls the maximum number of new TiDB pods started simultaneously during a scale-out.
- When the scale-out parallelism is set to N, the operator should attempt to bring up to N new TiDB instances online during each reconciliation loop.
- If any of those N instances are blocked (e.g., because a PVC with a deferred deletion annotation exists), only the unblocked ones advance; the others are retried on the next loop.
- The scale-out parallelism applies consistently whether or not the advanced scheduling feature with delete slot support is enabled.
- Similarly, a "scale-in parallelism" setting should control how many TiDB pods can be removed simultaneously in a single reconciliation loop, grouping the removed pods' PVCs with the same scale-in timestamp to indicate they were removed together.

## Why This Matters

Large TiDB deployments that need to scale out quickly are unnecessarily slowed down without this feature: each reconciliation loop adds only one pod. By allowing multiple pods to be started simultaneously, operators can scale out TiDB components as efficiently as TiFlash and TiKV components already support.
