Implement a mechanism to automatically reclaim inactive slot allocations when a job's resource requirements drop to zero in a distributed job execution system. Ensure that when a job manager disconnects, the job's final status is included in the disconnection notification. Update the slot tracker to query which worker nodes have slots allocated for a given job.

Requirements:

*   Update the `SlotTracker` interface:
    *   Declare a method `getTaskExecutorsWithAllocatedSlotsForJob(JobID jobId) -> Collection<TaskExecutorConnection>` to query task executors with slots in `PENDING_ALLOCATION` or `ALLOCATED` state for a given job.

*   Implement the method in `DefaultSlotTracker`:
    *   Return an empty collection if no slots are tracked, if slots are free, or if no slots are allocated to the queried job.
    *   Return `TaskExecutorConnection` for slots in `PENDING_ALLOCATION` or `ALLOCATED` state after `notifyAllocationStart` or `notifyAllocationComplete` is called.
    *   Return empty after `notifyFree` is called on a slot.

*   Update the `TaskExecutorGateway` interface:
    *   Declare a method `freeInactiveSlots(JobID jobId, Time timeout) -> void` to instruct task executors to release all inactive slots for a specific job.

*   Implement the method in `TaskExecutor`:
    *   Release all slots allocated to the given job that are inactive (no active job master connection).
    *   Notify the resource manager that the slot is available, reporting the correct `SlotID` and `AllocationID`.

*   Update the `ResourceManagerGateway` interface:
    *   Modify the `disconnectJobManager` method to accept `JobID jobId, JobStatus jobStatus, Exception cause` as parameters.

*   Update `DeclarativeSlotManager`:
    *   In `processResourceRequirements`, trigger `freeInactiveSlots` on task executor gateways when a job's requirements are empty (zero slots).
    *   Use `SlotTracker.getTaskExecutorsWithAllocatedSlotsForJob` to determine which task executors to notify.
    *   Do not call `freeInactiveSlots` when requirements are non-empty, even if reduced.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.