## Description

When a job is removed or its resource requirements drop to zero, slots that were reserved on worker nodes but never activated remain stuck in a lingering allocated state. These "ghost" allocations prevent other jobs from using those resources, but because no active connection was established between the job master and the worker, the slots are never properly released. The resource manager has no current mechanism to proactively reclaim such inactive slots when a job no longer needs them.

Additionally, when a job manager disconnects from the resource manager, the disconnection currently carries no information about the final status of the job. This limits the ability of downstream components to make informed decisions during cleanup.

## Expected Behavior

- When a job's resource requirements are set or reduced but remain non-empty, no slot reclamation should be triggered.
- When a job's resource requirements are cleared (set to empty/zero), the resource manager should instruct all relevant worker nodes to release any slots allocated but not activated for that job.
- Worker nodes must support a new operation to release inactive slots for a specified job, reporting freed slots back to the resource manager with their correct identifiers.
- The slot tracker must be queryable to find which worker nodes hold allocated slots for a given job, covering both the pending-allocation and fully-allocated states.
- When a job manager disconnects, the job's final status must be passed as part of the disconnection notification so that the resource manager and other listeners can act accordingly.

## Why This Matters

Without reclaiming inactive slot allocations, resources can leak whenever a job finishes or is cancelled before its job master ever connected to a worker. This causes unnecessary resource waste and can prevent other jobs from scheduling. Passing job status on disconnect enables smarter resource recovery.
