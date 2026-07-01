## Description

The VPA admission controller currently considers container-level resource limit ranges when generating recommendations, but it does not account for pod-level resource constraints. In clusters where administrators have defined minimum or maximum resource totals at the pod level, VPA-adjusted pods may end up violating those constraints — either because the sum of recommended resources across all containers exceeds the pod's maximum, or because it falls short of the pod's minimum.

## Expected Behavior

- When VPA computes resource recommendations for containers in a pod, it should also check whether the aggregate resources across all containers would comply with any pod-level limit range in the pod's namespace.
- If the aggregate exceeds the pod-level maximum for a resource, each container's recommendation should be scaled down proportionally so the total equals the allowed maximum.
- If the aggregate falls below the pod-level minimum for a resource, each container's recommendation should be scaled up proportionally so the total reaches the required minimum.
- If the aggregate already satisfies the pod-level constraints, the recommendations should be left unchanged.
- The mechanism for fetching pod-level limit ranges must be cleanly separated from the mechanism for fetching container-level limit ranges.

## Why This Matters

Without pod-level limit range awareness, VPA can produce recommendations that are individually valid for each container but collectively invalid for the pod as a whole. This leads to admission failures or policy violations that cluster operators must correct manually. Supporting pod-level constraints makes VPA's recommendations always valid at both the container and pod level.
