Implement automatic span management for streaming operations in the telemetry tracing system and provide a utility for safely preparing telemetry attributes. Ensure that spans close automatically when streams finish, and implement a utility function to handle and truncate telemetry values. Add a configuration option to control the inclusion of prompt content in telemetry spans.

*   Update the `truncateForTelemetry` function in `packages/core/src/telemetry/trace.ts`:
    *   Export `truncateForTelemetry` with the signature: `truncateForTelemetry(value: unknown, maxLength?: number): AttributeValue | undefined`.
    *   Return strings unchanged if their length is within `maxLength`.
    *   Truncate strings exceeding `maxLength` at grapheme-cluster boundaries, appending '...[TRUNCATED: original length N]'.
    *   Handle multi-byte unicode characters correctly during truncation.
    *   For objects, JSON.stringify them and apply the same truncation logic.
    *   Return numbers and booleans unchanged.
    *   Return `undefined` for `undefined`, functions, or symbols.

*   Modify the `runInDevTraceSpan` function in `packages/core/src/telemetry/trace.ts`:
    *   Update the signature to: `runInDevTraceSpan<R>(opts: SpanOptions & { operation: GeminiCliOperation; logPrompts?: boolean }, fn: ({ metadata }: { metadata: SpanMetadata }) => Promise<R>): Promise<R>`.
    *   Ensure the callback `fn` receives only `{ metadata: SpanMetadata }`.
    *   Remove the `noAutoEnd` option and replace it with an optional `logPrompts` boolean property.
    *   Automatically wrap async iterables returned by the callback, ending the span only after iteration completes or throws an error.
    *   Re-throw errors encountered during iteration after ending the span.

*   Update the `Config` interface:
    *   Include a method `getTelemetryLogPromptsEnabled(): boolean` to determine if prompt content should be logged in telemetry spans.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.