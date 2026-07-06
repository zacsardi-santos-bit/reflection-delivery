## Description

When a virtual machine is running, there is currently no easy way to see from the running instance itself which version of the VM's configuration it corresponds to. The VM object tracks its own generation and observed/desired generation in its status, but the running VM instance (VMI) carries no such information. This makes it difficult for operators and tools to determine whether the running instance reflects the latest configuration or an earlier one.

Additionally, when a VM is undergoing volume migration (moving storage to a new target volume), snapshots of that VM may incorrectly record the old volume names rather than the current migration destination names. This causes the snapshot content to not accurately reflect the running VM's storage configuration.

## Expected Behavior

- When a new VMI is started, it should receive an annotation recording the VM's current generation number.
- When the VM's configuration is updated in ways that only affect non-template fields (such as run strategy), the generation annotation on the running VMI should be updated automatically without requiring a restart.
- When the VM's template spec itself changes, the annotation should remain at its previous value, reflecting that the running instance has not yet adopted the new spec.
- The VM controller's generation tracking status fields (observed and desired generation) should be derived from and kept in sync with this annotation.
- When a generation annotation is missing or malformed on the VMI, the controller should back-fill it from the recorded controller revision.
- Snapshot creation should correctly capture the current VM volumes, disks, and data volume templates even when volume migration has changed the volume names from what is recorded in the controller revision.

## Why This Matters

Without a generation annotation on the VMI, users cannot tell from the running instance whether it is current or stale with respect to the VM's spec. Adding this annotation and keeping it in sync closes the observability gap between the VM desired state and what is actually running. The snapshot fix ensures that snapshots accurately capture the current storage state of a VM that is mid-migration, rather than the pre-migration state.
