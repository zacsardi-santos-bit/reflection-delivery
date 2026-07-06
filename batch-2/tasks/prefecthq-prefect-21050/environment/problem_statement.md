## Description

When a Kubernetes pod running a Prefect flow run encounters infrastructure problems, users currently have little visibility into what went wrong. The flow run either stays stuck or crashes without any indication of whether the issue was a bad container image, a memory limit being exceeded, the container repeatedly crashing, the cluster lacking capacity to schedule the pod, or the pod being evicted from its node. This lack of visibility makes it difficult to diagnose and resolve these issues quickly.

In addition, when a pod is still being provisioned (sitting in a pending state), the corresponding flow run does not reflect this — users cannot tell whether infrastructure is actively being set up or if something has stalled.

## Expected Behavior

- When a Kubernetes pod is in a pending state, the associated flow run should transition to an intermediate pending state so users know infrastructure is still being provisioned. This should only happen once — if the flow run has already moved past this state, no re-transition should occur.
- Common infrastructure failure patterns should be automatically detected from pod status information and surfaced as structured, actionable log messages directly on the flow run. These include:
  - Image pull failures (the image cannot be retrieved from the registry)
  - Out-of-memory kills (the container was terminated because it exceeded its memory limit)
  - Crash loops (the container is repeatedly crashing after starting)
  - Scheduling failures (the cluster cannot find a node with sufficient resources)
  - Evictions (the pod was removed from its node due to resource pressure)
- Failure diagnostics should include both an explanation of what happened and guidance on how to resolve it.
- If the same failure condition persists across multiple events for the same pod, the diagnostic log should only be emitted once — not repeatedly. If the pod recovers and then fails again, the diagnostic should be logged again.
- Failures on init containers should be diagnosed the same way as regular containers.

## Why This Matters

Users running flows on Kubernetes currently have to manually inspect pod status in their cluster to understand what went wrong. Surfacing this information directly in the Prefect flow run logs reduces the time to diagnosis and gives users actionable next steps without requiring Kubernetes expertise.
