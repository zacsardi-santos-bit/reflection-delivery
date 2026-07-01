## Description

The payment modification operations (capture, cancel, cancel-or-refund, refund) lack unit tests that verify the response parsing logic works correctly. Without these tests, it is difficult to confirm the library properly interprets payment processor responses, including handling both valid JSON payloads and malformed/error responses.

Additionally, there is currently no support for adjusting a previously authorized payment amount. This is a standard payment operation that allows merchants to modify the amount they intend to capture before the actual capture takes place, without requiring a completely new authorization.

## Expected Behavior

- Each modification operation (capture, cancel, cancel-or-refund, refund) should have its response correctly parsed from JSON, exposing a payment reference identifier and a status string from the processor.
- When the response body is invalid or cannot be parsed, the response parsing should return an error rather than silently succeed.
- A new adjust-authorisation operation should be supported, with a corresponding response type that includes a payment reference identifier and a status string.

## Why This Matters

Merchants sometimes need to adjust the amount of a pre-authorization before capturing it — for example, when a hotel stay is extended or a final bill differs from the initial estimate. Without this feature, developers using this library must fall back to alternative flows. Adding unit tests for all modification response parsers also ensures correctness and makes future refactoring safer.
