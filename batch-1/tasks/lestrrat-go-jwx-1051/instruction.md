Implement a fix for the JWS library to handle malformed JSON JWS messages without panicking and to distinguish verification errors from other errors. Ensure parsing succeeds for JSON messages missing a protected header, and provide a utility to identify cryptographic verification failures.

*   Update the `jws.Parse` function:
    *   Ensure it successfully parses JSON-serialized JWS messages with missing or empty 'protected' header fields without returning an error or panicking.
    *   Ensure it returns an error when parsing compact JWS serialization with an empty header segment.

*   Update the `jws.Verify` function:
    *   Ensure it returns an error when verifying JSON-serialized JWS messages with missing or empty 'protected' header fields, even if a valid key is provided and the 'signature' field contains a value.

*   Implement the `jws.IsVerificationError` function in `jws/jws.go`:
    *   Signature: `func IsVerificationError(err error) bool`
    *   Ensure it returns `true` if the error is caused by a cryptographic signature verification failure (wrapping a verifyError).
    *   Ensure it returns `false` for all other error types, including structural/format errors and errors unrelated to the signature verification.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.