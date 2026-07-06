Implement support for both the v1alpha1 and v1beta1 versions of the Kubernetes VolumeSnapshot API. Ensure the system can detect the available API version at runtime and return the appropriate snapshotter implementation. Provide explicit constructors for each API version and ensure the factory function returns an error if no supported API is available.

*   Modify `NewSnapshotter` function in `pkg/kube/snapshot/snapshot.go`:
    *   Change signature to `NewSnapshotter(kubeCli kubernetes.Interface, dynCli dynamic.Interface) (Snapshotter, error)`.
    *   Use the discovery API to detect available VolumeSnapshot API versions.
    *   Return a `*snapshot.SnapshotAlpha` and `nil` error if `v1alpha1` is available.
    *   Return a `*snapshot.SnapshotBeta` and `nil` error if `v1beta1` is available.
    *   Return `nil` and a non-nil error if neither version is available.

*   Implement `NewSnapshotAlpha` and `NewSnapshotBeta` functions:
    *   `NewSnapshotAlpha` in `pkg/kube/snapshot/snapshot_alpha.go` should return a `Snapshotter` backed by the `v1alpha1` API.
    *   `NewSnapshotBeta` in `pkg/kube/snapshot/snapshot_beta.go` should return a `Snapshotter` backed by the `v1beta1` API.

*   Ensure `SnapshotAlpha` and `SnapshotBeta` classes implement the `Snapshotter` interface:
    *   Implement methods: `Create`, `Get`, `Delete`, `Clone`, `GetVolumeSnapshotClass`, `GetSource`, `CreateFromSource`, `WaitOnReadyToUse`.
    *   Use respective API group's GVR variables for dynamic client interactions.

*   Create helper functions for unstructured API objects:
    *   `UnstructuredVolumeSnapshotClassAlpha`, `UnstructuredVolumeSnapshotContentAlpha`, `UnstructuredVolumeSnapshotAlpha` in `pkg/kube/snapshot/snapshot_alpha.go` for `v1alpha1`.
    *   `UnstructuredVolumeSnapshotClassBeta`, `UnstructuredVolumeSnapshotContentBeta`, `UnstructuredVolumeSnapshotBeta` in `pkg/kube/snapshot/snapshot_beta.go` for `v1beta1`.

*   Define and export necessary types and variables:
    *   In `pkg/kube/snapshot/apis/v1alpha1/types.go`, export `VolSnapGVR`, `VolSnapClassGVR`, `VolSnapContentGVR` for `v1alpha1`.
    *   Create `pkg/kube/snapshot/apis/v1beta1/types.go` to export `VolSnapGVR`, `VolSnapClassGVR`, `VolSnapContentGVR` for `v1beta1` and define `VolumeSnapshotClass` and `VolumeSnapshotContent` types.

*   Implement `Clone` operation for both `SnapshotAlpha` and `SnapshotBeta`:
    *   Ensure the cloned `VolumeSnapshotContent` name starts with the clone's name prefix.
    *   Match the `DeletionPolicy` with the source `VolumeSnapshotClass`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.