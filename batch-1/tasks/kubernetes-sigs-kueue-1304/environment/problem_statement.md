## Description

When a job's pod specifications change after a workload has already been created for it, the system should detect this mismatch and handle it in a context-aware way. Currently, several behaviors are incorrect or incomplete:

1. **Vague event messages**: When stopping a job due to a workload mismatch, the system emits the same generic message regardless of whether an existing workload was available to restore pod templates from. There should be distinct messages: one when restoration from an existing workload is possible, and one when no workload exists at all and restoration cannot happen.

2. **Incomplete pod spec comparison**: The comparison of two pod specifications does not account for differences in tolerations. As a result, jobs whose tolerations have changed may not be detected as mismatched against their workloads, allowing scheduling inconsistencies. The comparison should always detect differences in both pod counts and tolerations.

3. **Missing update path for unadmitted jobs**: When a suspended job that has not yet been admitted has tolerations that differ from its associated workload, the system should update the workload to reflect the job's new tolerations rather than treating it as an unrecoverable mismatch. Currently this update path is missing.

## Expected Behavior

- Pod spec comparison must always check counts and tolerations.
- Stopping a job with an available (but mismatched) workload emits a message indicating pod template restoration is occurring.
- Stopping a job with no workload at all emits a message indicating restoration was not possible.
- A suspended unadmitted job whose tolerations changed causes the workload to be updated to match.
- An admitted running job whose tolerations changed causes the job to be stopped/reverted and the workload deleted.
- A suspended admitted job whose tolerations changed causes the workload to be deleted.

## Why This Matters

Without these fixes, operators cannot tell from events whether a job was stopped because restoration was attempted (using an existing workload) or because no workload existed. Additionally, changes to tolerations on suspended jobs are silently ignored or incorrectly handled, leading to scheduling inconsistencies.
