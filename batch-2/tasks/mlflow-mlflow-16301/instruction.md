Implement the core entity types for traces in the MLflow TypeScript SDK. Define types for trace lifecycle states, storage locations, trace metadata, and span data. Create a top-level trace class that integrates these components and supports serialization and deserialization.

*   Define the `TraceState` enum in `packages/typescript/src/core/entities/trace_state.ts` with:
    *   `STATE_UNSPECIFIED = 'STATE_UNSPECIFIED'`
    *   `OK = 'OK'`
    *   `ERROR = 'ERROR'`
    *   `IN_PROGRESS = 'IN_PROGRESS'`
*   Implement `fromOtelStatus` function in `trace_state.ts`:
    *   Map `SpanStatusCode.OK` to `TraceState.OK`
    *   Map `SpanStatusCode.ERROR` to `TraceState.ERROR`
    *   Map `SpanStatusCode.UNSET` or any other value to `TraceState.STATE_UNSPECIFIED`
*   Define the `TraceLocationType` enum in `packages/typescript/src/core/entities/trace_location.ts` with:
    *   `TRACE_LOCATION_TYPE_UNSPECIFIED = 'TRACE_LOCATION_TYPE_UNSPECIFIED'`
    *   `MLFLOW_EXPERIMENT = 'MLFLOW_EXPERIMENT'`
    *   `INFERENCE_TABLE = 'INFERENCE_TABLE'`
*   Create `TraceLocation` interface in `trace_location.ts`:
    *   Required `type` field of `TraceLocationType`
    *   Optional `mlflowExperiment` with `experimentId: string`
    *   Optional `inferenceTable` with `fullTableName: string`
*   Implement `createTraceLocationFromExperimentId` function in `trace_location.ts`:
    *   Return `TraceLocation` with `type = TraceLocationType.MLFLOW_EXPERIMENT`
    *   Set `mlflowExperiment.experimentId` to the provided string
    *   Set `inferenceTable` to `undefined`
*   Define `TraceInfo` class in `packages/typescript/src/core/entities/trace_info.ts`:
    *   Constructor accepts an object with fields: `traceId`, `traceLocation`, `requestTime`, `state`, and optional fields `requestPreview`, `responsePreview`, `clientRequestId`, `executionDuration`, `traceMetadata`, `tags`, `assessments`
    *   Expose all fields as instance properties
*   Define `TraceData` class in `packages/typescript/src/core/entities/trace_data.ts`:
    *   Constructor accepts an array of `ISpan` objects
    *   Expose `spans` as an instance property
*   Define `Trace` class in `packages/typescript/src/core/entities/trace.ts`:
    *   Constructor accepts `TraceInfo` and `TraceData`
    *   Expose `info` and `data` as instance properties
    *   Implement `toJson()` to serialize to a JSON object with `info` and `data` keys
    *   Implement static `fromJson(json)` to reconstruct a `Trace` instance from JSON
*   Modify `ISpan` interface in `packages/typescript/src/core/entities/span.ts`:
    *   Update `toJson()` return type to `SerializedSpan`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.