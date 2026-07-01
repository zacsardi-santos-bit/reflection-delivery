Implement the ability to specify an explicit parent span when creating new spans in a distributed tracing SDK. Update the span creation methods to accept an optional parent span, and simplify the API for manually-managed spans by removing the finish helper parameter.

*   Update the `StartSpanOptions` interface in `packages/types/src/startSpanOptions.ts`:
    *   Add an optional `parentSpan` field of type `Span`.

*   Modify the `startSpan` function in `packages/core/src/tracing/trace.ts`:
    *   Accept `StartSpanOptions` with an optional `parentSpan`.
    *   Set the created span's `parent_span_id` to the `parentSpan`'s span ID if provided.
    *   Ensure the created span is the active span within the callback.
    *   Ensure `getActiveSpan()` returns `undefined` after the callback.

*   Modify the `startSpanManual` function in `packages/core/src/tracing/trace.ts`:
    *   Accept `StartSpanOptions` with an optional `parentSpan`.
    *   Set the created span's `parent_span_id` to the `parentSpan`'s span ID if provided.
    *   Ensure the created span is the active span within the callback.
    *   Remove the separate finish-helper parameter; end the span by calling its own end method.
    *   Ensure `getActiveSpan()` returns `undefined` after the callback.

*   Modify the `startInactiveSpan` function in `packages/core/src/tracing/trace.ts`:
    *   Accept `StartSpanOptions` with an optional `parentSpan`.
    *   Set the created span's `parent_span_id` to the `parentSpan`'s span ID if provided.
    *   Ensure `getActiveSpan()` remains `undefined`.

*   Update the OpenTelemetry implementations in `packages/opentelemetry/src/trace.ts`:
    *   Ensure `startSpan`, `startSpanManual`, and `startInactiveSpan` respect the `parentSpan` option.
    *   Set `spanToJSON(createdSpan).parent_span_id` to `parentSpan.spanContext().spanId` if `parentSpan` is provided.
    *   Ensure the active span behavior matches the core implementation requirements.

*   Ensure CPU profiler sample timestamps are expressed in seconds:
    *   Converted timestamps must be greater than `Date.now() - 60000` and less than or equal to `Date.now()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.