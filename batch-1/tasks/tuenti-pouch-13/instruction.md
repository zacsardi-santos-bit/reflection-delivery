Refactor the renewal timing logic of the secret management system to compute an absolute point in time for secret renewal, ensuring deterministic behavior. Implement support for deriving renewal timing from TLS certificates embedded in secret data, using the certificate's validity period scaled by a configured ratio.

Requirements:

*   Define a new type alias `SecretData` as `map[string]interface{}` in `state.go`.
    *   Use `SecretData` for the `Data` field in the `SecretState` struct.
*   Remove the separate `TTL` integer field from `SecretState`.
    *   Implement `SecretState.TTL()` to dynamically extract the TTL value from the `Data` map.
    *   `TTL()` must return `(int, bool)`, handling `json.Number`, `int`, and `int64` types.
    *   Return `(0, false)` if `Data` is nil, the key is absent, or the value is not a recognized numeric type.
*   Implement `SecretState.Ratio()` to return `DurationRatio` or `DefaultSecretDurationRatio` if `DurationRatio` is zero.
*   Implement `SecretState.TimeToUpdate()` to return `(time.Time, bool)`.
    *   Ensure `TimeToUpdate()` is deterministic and does not use `time.Now()`.
    *   For a secret with 'ttl' in `Data` and `DurationRatio` 0.5, return `(Timestamp + 180s, true)`.
    *   For a secret with a 'certificate' in `Data`, compute TTU using the certificate's validity period scaled by `DurationRatio`.
    *   Return `(time.Time{}, false)` if no TTL or certificate is present.
*   Update `PouchState.NextUpdate()` to return `(*SecretState, time.Time)`.
    *   Return `(nil, time.Time{})` if no secrets have a known TTU.
    *   Return the `SecretState` with the earliest absolute TTU among known secrets.
*   Ensure the call site in `pouch.go` converts the returned `time.Time` to a duration using `time.Until()` before passing it to `time.After()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.