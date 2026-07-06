## Description

When the cluster autoscaler scales down nodes, it currently reports the outcome of node deletion as a single error value — either the deletion succeeded, or it failed with some generic error. This provides no structured insight into *what stage* of the deletion failed (marking the node, evicting pods, or removing it from the cloud provider), and no detail about *which individual pods* failed to evict or why.

This makes it very difficult for operators and downstream systems to understand why a node wasn't removed. For example, if pod eviction times out for some pods but not others, there's currently no way to know which pods timed out, which succeeded, and what errors were encountered.

## Expected Behavior

- Node deletion should return a structured result that categorizes the type of failure: whether the node could not be marked for deletion, whether pod eviction failed, or whether the cloud provider rejected the delete.
- When pod eviction is attempted, each pod's individual outcome should be tracked: whether it timed out, what error occurred (if any), and whether the eviction was ultimately successful.
- When checking for pod disappearance after eviction, different outcomes (API errors, pod still present, pod not found) should each be recorded accurately in the per-pod result.
- The node deletion result type and per-pod eviction results should be surfaced through the scale-down status reporting.

## Why This Matters

Without this level of detail, debugging scale-down failures requires sifting through logs instead of having structured, queryable data. Structured per-pod eviction results and a classified node deletion outcome allow monitoring tools and automated remediation systems to react intelligently to specific failure types rather than treating all failures the same.
