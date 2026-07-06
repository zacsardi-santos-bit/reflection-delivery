Implement a mechanism to allow immediate volume binding for CDI data volumes, even when using a storage class with deferred volume binding. Introduce an annotation to signal this request, ensuring the import controller can proceed with operations regardless of the global feature gate setting.

*   Update the `shouldReconcilePVC` function:
    *   Change the signature to: `shouldReconcilePVC(pvc *corev1.PersistentVolumeClaim, isImmediateBindingRequested bool, featureGates featuregates.FeatureGates, log logr.Logger) (bool, error)`.
    *   Return `false` if `isImmediateBindingRequested` is `false`, the `honorWaitForFirstConsumer` feature gate is enabled, and the PVC is in a pending state.
    *   Return `true` if `isImmediateBindingRequested` is `true`, regardless of the `honorWaitForFirstConsumer` feature gate status.
    *   Preserve existing behavior when `isImmediateBindingRequested` is `false` and the `honorWaitForFirstConsumer` feature gate is disabled.

*   Define and export a constant:
    *   `AnnImmediateBinding` in `pkg/controller/util.go` with the value `AnnAPIGroup + "/storage.bind.immediate.requested"`.
    *   Use this annotation key on a DataVolume/PVC to request immediate volume binding.

*   Modify the `NewDataVolumeForBlankRawImage` function:
    *   Initialize the `Annotations` field of the returned `DataVolume` as a non-nil empty map to allow safe addition of annotations.

*   Ensure DataVolumes with the `AnnImmediateBinding` annotation set to 'true':
    *   Reach the Bound state in a WaitForFirstConsumer environment.
    *   Progress to their expected terminal phase:
        *   `Succeeded` for HTTP imports and blank image imports.
        *   `Succeeded` for clone operations.
        *   `UploadReady` for upload operations.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.