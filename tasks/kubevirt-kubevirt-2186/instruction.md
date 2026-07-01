Implement enhancements to KubeVirt's live migration validation to include data volumes and provide an override for migration safety checks. Update test utilities to support both filesystem and block volume modes for iSCSI-backed storage.

*   Update the `MigrationConfig` struct:
    *   Add a boolean field `UnsafeMigrationOverride` with JSON key `unsafeMigrationOverride`.
    *   Ensure `GetMigrationConfig()` returns a config with `UnsafeMigrationOverride` set to true when specified in the cluster config JSON.

*   Update the `MigrationOptions` struct:
    *   Add a boolean field `UnsafeMigration`.
    *   Populate `UnsafeMigration` from `GetMigrationConfig().UnsafeMigrationOverride` during migration option construction.

*   Modify `prepareMigrationFlags` function:
    *   Accept two boolean parameters: `isBlockMigration` and `isUnsafeMigration`.
    *   Always include `MIGRATE_LIVE | MIGRATE_PEER2PEER` flags.
    *   Add `MIGRATE_NON_SHARED_INC` if `isBlockMigration` is true.
    *   Add `MIGRATE_UNSAFE` if `isUnsafeMigration` is true.

*   Enhance `checkVolumesForMigration` function:
    *   Evaluate both `PersistentVolumeClaim` and `DataVolume` sources on a VMI.
    *   Return `blockMigrate=true` and error "cannot migrate VMI with non-shared PVCs" for non-shared PVCs (e.g., `ReadWriteOnce`).

*   Reject migration requests for VMI with non-shared DataVolume PVC:
    *   Include error message containing 'DisksNotLiveMigratable'.

*   Update `NewRandomDataVolumeWithHttpImport` function:
    *   Accept a third parameter `accessMode` of type `k8sv1.PersistentVolumeAccessMode`.
    *   Use the provided `accessMode` in the DataVolume PVC spec.
    *   Set storage quantity to 1Gi.

*   Modify `CreateISCSIPvAndPvc` function:
    *   Accept a fourth parameter `volumeMode` of type `k8sv1.PersistentVolumeMode`.
    *   Pass `volumeMode` when creating the PV and PVC.

*   Update `NewISCSIPvAndPvc` function:
    *   Accept a fifth parameter `volumeMode` of type `k8sv1.PersistentVolumeMode`.
    *   Apply `volumeMode` when creating PV and PVC objects.

*   Add `CreateISCSIPV` function to `tests/utils.go`:
    *   Signature: `CreateISCSIPV(name, size, iscsiTargetIP string, accessMode k8sv1.PersistentVolumeAccessMode, volumeMode k8sv1.PersistentVolumeMode) *k8sv1.PersistentVolume`.
    *   Return a `PersistentVolume` object without creating a PVC.

*   Introduce `ContainerDiskEmpty` constant:
    *   Value: 'empty'.
    *   When used in `CreateISCSITargetPOD`, configure the pod container with `AS_EMPTY=true` environment variable.

*   Ensure migration with `unsafeMigrationOverride`:
    *   Allow VMI backed by shared iSCSI filesystem PVC (ReadWriteMany, filesystem volume mode) to migrate using the unsafe libvirt migration flag.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.