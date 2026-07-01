Implement a scale-out and scale-in parallelism feature for the TiDB component in a TiDB Operator-managed cluster. Ensure the TiDB component can control the number of instances that come online or are removed simultaneously during scaling operations, similar to existing functionality for TiFlash and TiKV.

*   Update the `TiDBSpec` type:
    *   Add a `ScalePolicy` field of type `ScalePolicy` to `TiDBSpec` in `pkg/apis/pingcap/v1alpha1/types.go`.
    *   Ensure `ScalePolicy` includes `ScaleInParallelism` and `ScaleOutParallelism` as optional `int32` pointer fields.
    *   Implement a deep copy for `ScalePolicy` in `TiDBSpec` using `in.ScalePolicy.DeepCopyInto(&out.ScalePolicy)`.

*   Implement methods in `pkg/apis/pingcap/v1alpha1/tidbcluster.go`:
    *   `GetScaleOutParallelism()`: Return the `ScaleOutParallelism` value from `TiDBSpec.ScalePolicy`, defaulting to 1 if nil.
    *   `GetScaleInParallelism()`: Return the `ScaleInParallelism` value from `TiDBSpec.ScalePolicy`, defaulting to 1 if nil.

*   Update the TiDB scaler's methods in `pkg/manager/member/tidb_scaler.go`:
    *   `ScaleOut` method:
        *   Use `GetScaleOutParallelism()` to determine the number of replicas to add per reconciliation.
        *   Use `scaleMulti` to compute ordinals for scaling.
        *   For each ordinal, if a blocking PVC exists, return an error and reset replicas to the old value. If no blocking PVC, advance replicas by the number of successful ordinals, up to `ScaleOutParallelism`, capped at the target.
        *   Handle delete slots correctly when AdvancedStatefulSet is enabled.
    *   `ScaleIn` method:
        *   Use `GetScaleInParallelism()` to determine the number of ordinals to scale in per reconciliation.
        *   Annotate PVCs with a scale-in timestamp for all ordinals scaled in the same round.
        *   Use `scaleMulti` to process multiple ordinals simultaneously up to the parallelism limit.

*   Ensure correct handling of blocking PVCs:
    *   With `ScaleOutParallelism=1` and a blocking PVC, return an error and maintain the old replica count.
    *   With `ScaleOutParallelism=1` and no blocking PVC, increment `newSet.Spec.Replicas` by 1.
    *   With `ScaleOutParallelism=2` and no blocking PVCs, increment `newSet.Spec.Replicas` by up to 2.
    *   With `ScaleOutParallelism=3` and no blocking PVCs, increment `newSet.Spec.Replicas` by up to 3.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.