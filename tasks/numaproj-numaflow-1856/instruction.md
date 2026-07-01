Implement a comprehensive health-check mechanism for pipelines and inter-step buffer services in the numaflow platform. Ensure that the health status accurately reflects the operational state beyond mere readiness, and update the pipeline status to distinguish between map-type and reduce-type user-defined functions.

*   Update `InterStepBufferServiceStatus`:
    *   Implement the `IsHealthy()` method in `pkg/apis/numaflow/v1alpha1/isbsvc_types.go`.
        *   Return `true` only when the phase is `ISBSvcPhaseRunning` and `IsReady()` returns `true`.
        *   Return `false` for any other phase or when conditions are not fully met.
    *   Modify any utility function that waits for an ISBSvc to be ready to use `IsHealthy()` instead of `IsReady()`.

*   Update `PipelineStatus`:
    *   Implement the `IsHealthy()` method in `pkg/apis/numaflow/v1alpha1/pipeline_types.go`.
        *   Return `false` for `PipelinePhaseFailed` and any unrecognized phase.
        *   Return the result of `IsReady()` for `PipelinePhaseRunning`.
        *   Return `true` for `PipelinePhaseDeleting`, `PipelinePhasePausing`, and `PipelinePhasePaused`.
    *   Add the `MapUDFCount` field to the `PipelineStatus` struct.
        *   Signature: `MapUDFCount *uint32 `json:"mapUDFCount,omitempty" protobuf:"varint,9,opt,name=mapUDFCount"`
        *   Populate using the `SetVertexCounts` method with the count of map-type UDF vertices.
    *   Add the `ReduceUDFCount` field to the `PipelineStatus` struct.
        *   Signature: `ReduceUDFCount *uint32 `json:"reduceUDFCount,omitempty" protobuf:"varint,10,opt,name=reduceUDFCount"`
        *   Populate using the `SetVertexCounts` method with the count of reduce-type UDF vertices.

*   Update the OpenAPI/Swagger specification:
    *   Modify `api/openapi-spec/swagger.json` to include `mapUDFCount` and `reduceUDFCount` as integer fields (format `int64`) in the pipeline status schema definition.

*   Ensure that after a successful ISBSvc reconciliation, the `IsHealthy()` method on the resulting status returns `true`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.