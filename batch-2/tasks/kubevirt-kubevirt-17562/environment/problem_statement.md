## Description

Several bugs in the KubeVirt live migration system need to be addressed, particularly around decentralized (non-shared-state) migrations. These issues cause migrations to fail, leave virtual machines stuck in intermediate states, and produce incorrect timing data when migrations fail.

## Problems

**1. Migration transport type not propagated to target**

During a decentralized migration, the connection transport type used by the source VM is never forwarded to the target node. As a result, the target node cannot correctly determine how to set up the migration proxy, causing migration setup to fail.

**2. Start time overwritten on failed migrations**

When the system marks a migration as failed, it always sets the start time to the current time — even if a start time was already recorded earlier in the migration lifecycle. This overwrites valid timing data and makes it impossible to determine the actual migration duration.

**3. Recovery annotation removed too early on failed migrations**

During cleanup of a failed migration target, a critical annotation that signals the virtual machine needs re-initialization is being removed. This prevents the VM from following its proper recovery path after the migration fails.

**4. Migration target VMI not transitioning to waiting state on pod failure**

A virtual machine instance in the scheduled phase that is a migration target should transition to a waiting/recovery state when its pod terminates or when the migration has definitively failed. Currently, certain conditions (a terminated pod with an existing pod object, or a failed migration with a still-running pod) are not handled, leaving the VMI stuck in the scheduled phase instead of re-entering the synchronization flow.

## Expected Behavior

- The transport type should be copied from the source VMI status to the target during migration synchronization.
- A pre-existing start timestamp should be preserved when marking a migration as failed; only the end timestamp and failure flags should be updated.
- After a failed migration cleanup, the recovery annotation should remain on the VMI.
- A migration target VMI should transition to the waiting-for-synchronization state whenever its pod becomes unavailable (failed or completed) or when the migration has failed, regardless of whether a pod object still exists.
- The patch mechanism for migration state updates should operate at the individual field level rather than replacing the entire object, to avoid conflicts with concurrent updates from other controllers.

## Why This Matters

Without these fixes, decentralized live migrations are unreliable: they can fail silently, leave VMs stranded in intermediate states, lose accurate timing data, or prevent proper VM recovery after migration failure.
