Implement response parsing for payment modification operations and add support for adjusting a previously authorized payment amount. Ensure each operation correctly parses JSON responses and handles errors for invalid input.

*   Implement the `capture()` method in the `Response` type:
    *   Parse the response body as JSON into a `CaptureResponse` struct.
    *   Ensure `CaptureResponse` includes `PspReference` and `Response` fields.
    *   Return an error if the body cannot be parsed.

*   Implement the `cancel()` method in the `Response` type:
    *   Parse the response body as JSON into a `CancelResponse` struct.
    *   Ensure `CancelResponse` includes `PspReference` and `Response` fields.
    *   Return an error if the body cannot be parsed.

*   Implement the `cancelOrRefund()` method in the `Response` type:
    *   Parse the response body as JSON into a `CancelOrRefundResponse` struct.
    *   Ensure `CancelOrRefundResponse` includes `PspReference` and `Response` fields.
    *   Return an error if the body cannot be parsed.

*   Implement the `refund()` method in the `Response` type:
    *   Parse the response body as JSON into a `RefundResponse` struct.
    *   Ensure `RefundResponse` includes `PspReference` and `Response` fields.
    *   Return an error if the body cannot be parsed.

*   Add support for the adjust-authorisation operation:
    *   Implement the `adjustAuthorisation()` method in the `Response` type.
    *   Parse the response body as JSON into an `AdjustAuthorisationResponse` struct.
    *   Ensure `AdjustAuthorisationResponse` includes `PspReference` and `Response` fields.
    *   Return a pointer to `AdjustAuthorisationResponse` and an error if the body cannot be parsed.

*   Define the `AdjustAuthorisationResponse` struct in `modification.go`:
    *   Include string fields `PspReference` and `Response`.

*   Ensure the `Response` struct includes:
    *   A `Response` field of type `*http.Response`.
    *   A `Body` field of type `[]byte` to hold pre-read raw response bytes.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.