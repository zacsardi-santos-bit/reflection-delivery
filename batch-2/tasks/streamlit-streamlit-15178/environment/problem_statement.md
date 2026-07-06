## Description

The authentication module needs a reliable way to create and validate short-lived provider tokens that identify which OAuth provider a user selected during sign-in. Right now the module depends on a single JWT library, but a newer version of that library introduced an optional sub-library as a transitive dependency — users on older installations won't have it, which would break token creation and validation for them.

We need a dual-backend design that automatically picks the best available JWT library and silently falls back to the older one when the newer sub-library isn't installed. If neither is available, the error shown to the user should clearly say how to install the necessary dependencies.

## Expected Behavior

- Encoding and decoding provider tokens should work on both new installs (with the newer JWT sub-library) and older installs (with only the original library).
- If neither JWT library is available, operations must fail with a clear error message pointing the user to the correct install command.
- Decoding must validate that tokens contain a non-empty provider name and a valid integer expiration timestamp, and must raise a clear error for expired tokens or missing/malformed claims.
- Internal library warnings about key length should not be surfaced to end users. Instead, a single informational message should be logged when the signing secret is shorter than the recommended 112-bit minimum — logged only once, not repeatedly.
- Suppressing those internal warnings must be idempotent — calling the suppression logic multiple times should not add duplicate warning filters.

## Why This Matters

Users who installed the authentication extra before the JWT sub-library was introduced would silently break on upgrade, and users on minimal installs would see confusing internal library warnings instead of a clear message about what to install.
