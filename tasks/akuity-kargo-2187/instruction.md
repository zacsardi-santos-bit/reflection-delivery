Extend the Stage status to track freight history in a structured manner, supporting scenarios where a stage aggregates freight from multiple warehouses. Implement a new history structure that maintains freight items keyed by warehouse name and update related operations to use this structure.

*   Add a new `FreightHistoryEntry` struct in `api/v1alpha1/types_stage.go`:
    *   Include a `Freight` field of type `map[string]FreightReference`.
    *   Implement `UpdateOrPush(freight ...FreightReference)` to update or append entries by warehouse name.

*   Define a `FreightHistory` type as `[]*FreightHistoryEntry` in `api/v1alpha1/types_stage.go`:
    *   Implement a `Current()` method returning the most recent entry or `nil`.

*   Update the `StageStatus` struct in `api/v1alpha1/types_stage.go`:
    *   Add a `FreightHistory` field of type `FreightHistory`.
    *   Retain `CurrentFreight` and `History` fields as deprecated.
    *   Ensure `CurrentPromotion` and `LastPromotion` fields are present.

*   Modify control flow stages:
    *   Clear `FreightHistory`, `CurrentPromotion`, `LastPromotion`, `CurrentFreight`, and `History` fields when syncing.

*   Sync promotions:
    *   Access current freight via `status.FreightHistory.Current().Freight[warehouseName]`.
    *   Ensure `status.FreightHistory` is empty if no terminal promotions succeed.

*   Update reconciler methods in `internal/controller/stages/verification.go`:
    *   `startVerification(ctx context.Context, stage *kargoapi.Stage, freightRef kargoapi.FreightReference) (*kargoapi.VerificationInfo, error)`: Use `freightRef` instead of `stage.Status.CurrentFreight`.
    *   `getVerificationInfo(ctx context.Context, stage *kargoapi.Stage, freightRef kargoapi.FreightReference) (*kargoapi.VerificationInfo, error)`: Read from `freightRef`.
    *   `abortVerification(ctx context.Context, stage *kargoapi.Stage, freightRef kargoapi.FreightReference) *kargoapi.VerificationInfo`: Use `freightRef`.
    *   `buildAnalysisRun(stage *kargoapi.Stage, verificationInfo *kargoapi.VerificationInfo, freight *kargoapi.Freight, templates []*rollouts.AnalysisTemplate) (*rollouts.AnalysisRun, error)`: Use `freight.Name` for `FreightLabelKey`.

*   Update helper functions in `api/v1alpha1`:
    *   `ReverifyStageFreight` and `AbortStageFreightVerification` must use `stage.Status.FreightHistory.Current()`.
    *   Return an error if no current freight is found.

*   Update the indexer in `internal/kubeclient`:
    *   `IndexStagesByAnalysisRun` must iterate over `stage.Status.FreightHistory` entries.

*   Ensure `FreightReference` struct includes a `Warehouse` field for matching in `UpdateOrPush`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.