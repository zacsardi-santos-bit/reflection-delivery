I'm working on fixing several bugs in the KubeVirt live migration system related to decentralized migrations.

*   The SyncSourceMigrationStatus function must copy the MigrationTransport field from the remote VMI status into the local VMI Status.MigrationTransport and persist it via the patching mechanism.

*   The addMigrationStateFieldPatches function must accept a patch set and two MigrationState pointers (original and new). For each field that changes from a zero value to a non-zero value, it must add an 'op':'add' JSON Patch operation at the path '/status/migrationState/<fieldName>'. For each field that changes from a non-zero value to a different non-zero value, it must add a 'op':'test' operation (asserting the old value) followed by an 'op':'replace' operation (setting the new value).

*   The addMigrationStateFieldPatches function must skip fields that are unchanged between the original and new MigrationState — no patch operations should be generated for those fields.

*   When both the original and new MigrationState are identical (nothing changed), addMigrationStateFieldPatches must produce no patch operations — the patch set must remain empty.

*   When the new MigrationState is nil (the field is being removed), addMigrationStateFieldPatches must generate an 'op':'test' operation asserting the original value followed by an 'op':'remove' operation, both targeting the '/status/migrationState' path.

*   When marking a failed migration on a VMI, if the VMI's MigrationState already has a non-nil StartTimestamp, it must be preserved unchanged. The EndTimestamp must always be set to the current time, and Completed and Failed must be set to true.

*   A VMI in the Scheduled phase that is marked as a migration target must transition to WaitingForSync phase when its pod is in a Failed or Succeeded (terminal) state.

*   A VMI in the Scheduled phase that is marked as a migration target must transition to WaitingForSync phase when the migration has already failed (Failed=true and Completed=true in MigrationState), even if the pod is still Running.

*   A VMI already in the WaitingForSync phase that is marked as a migration target must remain in WaitingForSync phase when its pod is in a Failed or Succeeded state.

*   A VMI already in the WaitingForSync phase that is marked as a migration target must remain in WaitingForSync phase when the migration has failed, even if the pod is still Running.

*   A Running VMI that is NOT a migration target must transition to the Failed phase when its pod disappears.

*   The migrationProxyKey function must accept a VirtualMachineInstance and return a string key. When the VMI is a migration target, uses UNIX transport (MigrationTransport == MigrationTransportUnix), has a non-nil MigrationState, a non-nil SourceState, and a non-nil VirtualMachineInstanceUID in SourceState, it must return the source VMI UID string. In all other cases (non-UNIX transport, nil MigrationState, nil SourceState, or nil VirtualMachineInstanceUID), it must return the VMI's own UID as a string.

*   During migration target cleanup after a FAILED migration, the MigrationTargetNodeNameLabel label must be removed but the CreateMigrationTarget annotation must be preserved (not deleted).

*   During migration target cleanup after a SUCCESSFUL (non-failed, completed) migration, both the MigrationTargetNodeNameLabel label and the CreateMigrationTarget annotation must be removed.


*   Interface details: Type: Function
Name: addMigrationStateFieldPatches
Location: pkg/synchronization-controller/synchronization-controller.go
Signature: addMigrationStateFieldPatches(patchSet *patch.PatchSet, origMS, newMS *virtv1.VirtualMachineInstanceMigrationState)
Description: Generates individual JSON Patch operations for each changed field in MigrationState. For fields transitioning from zero to non-zero, emits an "add" operation at path "/status/migrationState/<fieldName>". For fields transitioning from non-zero to a different non-zero, emits a "test" (asserting old value) followed by a "replace" (setting new value). Unchanged fields are skipped. If newMS is nil, emits a "test" + "remove" for the entire "/status/migrationState" path.

Type: Function
Name: migrationProxyKey
Location: pkg/virt-handler/migration-target.go
Signature: migrationProxyKey(vmi *v1.VirtualMachineInstance) string
Description: Returns the UID string to use as the migration proxy lookup key. Returns the source VMI UID (from MigrationState.SourceState.VirtualMachineInstanceUID) when the VMI is a migration target, MigrationTransport is MigrationTransportUnix, MigrationState is non-nil, SourceState is non-nil, and VirtualMachineInstanceUID is non-nil. Returns the VMI's own UID string in all other cases.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.