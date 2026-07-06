## Description

The document conversion service client has several gaps that make it harder to use reliably in production:

1. **Task failures lack structured detail.** When a conversion task fails on the server, the client only surfaces a plain string error message. The server already tracks richer information about failures — the category of failure, which processing phase it occurred in, and whether the caller should retry — but none of that is modeled in the client response types or surfaced through a dedicated exception.

2. **Option serialization includes unwanted defaults.** When submitting a conversion request with only a subset of options explicitly set, the serialized payload includes fields at their default values (e.g., empty values for unset optional fields like timeouts). This inflates the request unnecessarily and can trigger serialization warnings in the underlying validation library.

3. **No automatic delivery target selection.** When no result delivery target is specified, the client should intelligently pick the most efficient available method — preferring presigned URL delivery when the server supports it, and falling back to inline body delivery if not. Currently this fallback logic is absent from batch retrieval flows.

4. **Schema mismatches produce generic errors.** If a client and server are running mismatched versions and the response format has changed, the client raises a generic unhandled error rather than a clear, actionable exception that communicates the likely cause (version skew).

## Expected Behavior

- Structured failure information (category, phase, retryability) should be modeled in the response layer and a specific exception type raised when a task fails execution, carrying that information.
- Serializing conversion options should only include explicitly set, non-default values, and must be warning-free.
- Batch retrieval via the item-by-item submission flow should support both explicit target selection and automatic target selection with presigned-first fallback.
- When the response from the server does not match the expected schema, a dedicated exception with an informative message should be raised instead of a generic one.

## Why This Matters

These improvements make failure handling more actionable for callers (they can inspect failure metadata to decide whether to retry), keep request payloads clean, and provide clear diagnostics when client and server versions drift apart.
