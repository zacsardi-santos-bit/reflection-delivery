Implement the foundational entity classes and utility functions for tracing spans in the TypeScript package for MLflow. Ensure that spans can be created, manipulated, serialized, and deserialized according to the specified requirements.

*   Implement the `createMlflowSpan` function in `packages/typescript/src/core/entities/span.ts`:
    *   Return a `NoOpSpan` when the first argument is `null` or `undefined`.
    *   Return a `LiveSpan` when the span is active.
    *   Return a read-only `Span` when the span is completed.

*   Define the `Span` class in `packages/typescript/src/core/entities/span.ts`:
    *   Implement `toJson()` and `fromJson(serializedSpan)` methods.
    *   Ensure serialization and deserialization preserve all specified properties.

*   Define the `NoOpSpan` class in `packages/typescript/src/core/entities/span.ts` with fixed properties.

*   Implement the `LiveSpan` interface in `packages/typescript/src/core/entities/span.ts`:
    *   Include methods: `setInputs`, `setOutputs`, `setAttribute`, `setStatus`, `addEvent`.

*   Define the `SpanEvent` class in `packages/typescript/src/core/entities/span_event.ts`:
    *   Implement constructor and `fromException(error)` method.
    *   Implement `toJson()` method.

*   Define the `SpanStatus` class in `packages/typescript/src/core/entities/span_status.ts`:
    *   Implement constructor, `toJson()`, and `toOtelStatus()` methods.

*   Define the `SpanStatusCode` enum in `packages/typescript/src/core/entities/span_status.ts` with values: `OK`, `ERROR`, `UNSET`.

*   Implement utility functions in `packages/typescript/src/core/utils/index.ts`:
    *   `convertNanoSecondsToHrTime(nanos: number): HrTime`
    *   `convertHrTimeToNanoSeconds(hrTime: HrTime): bigint`
    *   `encodeSpanIdToBase64(spanId: string): string`
    *   `encodeTraceIdToBase64(traceId: string): string`
    *   `decodeIdFromBase64(base64Id: string): string`

*   Define constants in `packages/typescript/src/core/constants.ts`:
    *   `SpanAttributeKey` with keys for MLflow attributes.
    *   `SpanType` with values: `LLM`, `CHAIN`.

*   Use `JSONBig` in `packages/typescript/src/core/utils/json.ts` for BigInt serialization/deserialization.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.