I'm working on the live VM migration subsystem and have found several related issues that need to be fixed together.

*   The synchronization controller's source migration status sync operation must propagate the MigrationTransport value from the remote VMI status to the local VMI's Status.MigrationTransport field.

*   addMigrationStateFieldPatches must generate 'add' patch operations (not 'test') for migration state fields that are zero in the original object but non-zero in the new object; the patch path must be /status/migrationState/<camelCase field name> (e.g. /status/migrationState/targetNode, /status/migrationState/targetPod).

*   addMigrationStateFieldPatches must generate 'test' followed by 'replace' patch operations for migration state fields that are non-zero in both the original and new objects but have different values.

*   addMigrationStateFieldPatches must skip (produce no patch operations for) migration state fields whose values are identical in both the original and new objects.

*   addMigrationStateFieldPatches must produce an empty patch set when the original and new migration state objects are identical (patchSet.IsEmpty() returns true).

*   addMigrationStateFieldPatches must generate a 'test' operation followed by a 'remove' operation at the path /status/migrationState when newMS is nil, operating on the entire migration state object rather than individual fields.

*   When a migration transitions to a failed state, the migration controller must set EndTimestamp on the VMI's MigrationState and set Completed=true and Failed=true, without overwriting an existing StartTimestamp value.

*   A VMI that is a migration target (has the CreateMigrationTarget annotation) and is in the Scheduled phase must transition to WaitingForSync phase when its associated pod is in a terminal state (Failed or Succeeded).

*   A VMI that is a migration target and is in the Scheduled phase must transition to WaitingForSync phase when the migration has completed with failure (Completed=true, Failed=true), regardless of whether the pod is still running.

*   A VMI that is a migration target and is already in WaitingForSync phase must remain in WaitingForSync when its pod is in a terminal state (Failed or Succeeded).

*   A VMI that is a migration target and is already in WaitingForSync phase must remain in WaitingForSync when the migration has completed with failure (Completed=true, Failed=true) even if the pod is running.

*   A VMI in Running phase that is not a migration target and has no associated pod must transition to the Failed phase.

*   On failed migration cleanup, the migration target handler must remove the MigrationTargetNodeNameLabel label from the VMI but must preserve the CreateMigrationTarget annotation.

*   On successful migration cleanup, the migration target handler must remove both the MigrationTargetNodeNameLabel label and the CreateMigrationTarget annotation from the VMI.

*   migrationProxyKey must return the source VMI UID (from SourceState.VirtualMachineInstanceUID) when the VMI has the CreateMigrationTarget annotation, the MigrationTransport is MigrationTransportUnix, and SourceState.VirtualMachineInstanceUID is non-nil; in all other cases (no annotation, non-Unix transport, nil MigrationState, nil SourceState, or nil VirtualMachineInstanceUID) it must return the local VMI UID as a string.


*   Interface details: Type: Function
Name: addMigrationStateFieldPatches
Location: pkg/synchronization-controller/synchronization-controller.go
Signature: addMigrationStateFieldPatches(patchSet *patch.PatchSet, origMS *virtv1.VirtualMachineInstanceMigrationState, newMS *virtv1.VirtualMachineInstanceMigrationState)
Description: Generates JSON patch operations comparing two VirtualMachineInstanceMigrationState objects and adds them to the provided patchSet. For each field that changed from zero value to a non-zero value, adds an "add" operation at the path /status/migrationState/<fieldName>. For fields that are non-zero in both but differ, adds "test" then "replace" operations. Unchanged fields are skipped. When newMS is nil, generates "test" + "remove" operations at /status/migrationState for the whole object. The patchSet.IsEmpty() returns true when no fields changed.

Type: Function
Name: migrationProxyKey
Location: pkg/virt-handler/migration-target.go
Signature: migrationProxyKey(vmi *virtv1.VirtualMachineInstance) string
Description: Returns the key to use when looking up the migration proxy for a given VMI. For regular migrations, returns the local VMI UID as a string. For decentralized UNIX transport migrations (when the VMI has the CreateMigrationTarget annotation set, MigrationTransport is MigrationTransportUnix, and SourceState.VirtualMachineInstanceUID is non-nil), returns the source VMI UID as a string. In all other cases (non-UNIX transport, missing SourceState, nil MigrationState, or nil VirtualMachineInstanceUID), returns the local VMI UID as a string.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.