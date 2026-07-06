## Description

When using the OpenRouter gateway to route AI model requests, certain real-world response shapes from downstream providers cause the client to crash with an internal validation error rather than being handled correctly.

There are two known problematic response patterns:

1. **Nested provider response**: OpenRouter sometimes returns a response where all the top-level standard fields (such as the list of generated choices, model name, and response ID) are null, and the actual completion data is embedded inside the provider metadata as a nested dict. Currently, this causes an unhandled validation failure instead of the response being successfully unwrapped and returned.

2. **Error response with null standard fields**: When a downstream provider fails, OpenRouter may return an error object (containing a numeric status code and message) while leaving all the standard completion fields null. Currently, this also triggers a confusing internal validation error instead of a properly typed HTTP error being raised with the correct status code.

## Expected Behavior

- When the actual completion data is nested inside the provider metadata dict, it should be automatically detected and unwrapped, producing a normal response.
- When the nested provider name is null or missing, the downstream provider should fall back to the value "unknown".
- When a provider dict is present but cannot be interpreted as a nested completion (missing choices), the existing validation error behavior should be preserved.
- When an error object is present alongside null standard fields, a typed HTTP error should be raised with the correct numeric status code and the error message text.
- When the error field exists but is malformed (not a dict), the existing unexpected-model-behavior error path should be preserved.
- Extra fields in the error object (such as metadata) should be silently ignored.

## Why This Matters

These response shapes appear in real production traffic (see issue #3994) when downstream providers time out or return errors via the OpenRouter gateway. Without this fix, users experience cryptic internal errors that give no indication of the underlying provider failure.
