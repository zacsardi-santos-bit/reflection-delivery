## Description

The deployment configuration controller is not correctly tracking whether it has observed the latest version of a deployment configuration's spec. Currently, the controller always overwrites the "observed generation" in the status with the current metadata generation, regardless of context. This is incorrect — in certain situations (such as when the controller encounters an error mid-reconciliation or when a configuration is paused), it should not report that it has observed the latest generation until it has actually finished processing the change.

## Expected Behavior

- When the controller fully reconciles a deployment configuration, it should update the status to reflect that it has observed the current generation.
- When the controller cannot fully process a configuration change (e.g., due to an error or a condition where work is deferred), the observed generation in the status should remain at its previous value rather than being unconditionally advanced.
- The function that computes a new deployment config status should accept an explicit flag indicating whether the observed generation should be advanced, and only update it when that flag is set.

## Why This Matters

Without this fix, tooling and operators cannot reliably determine whether the controller has caught up to a configuration change. For example, after pausing a deployment and then wanting to confirm the controller acknowledged the pause, there is no trustworthy signal. Automations that wait for the observed generation to advance before proceeding may behave incorrectly because the controller reports generation advancement even when it hasn't finished processing the change.
