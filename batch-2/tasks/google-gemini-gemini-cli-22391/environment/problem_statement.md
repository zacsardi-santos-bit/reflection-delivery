## Description

On systems where the native OS keychain is not available — such as headless servers, certain CI environments, or when the developer explicitly wants to avoid the native keychain — the credential storage service currently reports itself as unavailable and throws errors on every credential operation. This makes it impossible to store or retrieve credentials in those environments.

## Expected Behavior

- The credential storage service should always report itself as available, even when the native keychain cannot be used, by transparently falling back to an encrypted file-based storage backend.
- When the fallback is active, a debug log message should be emitted to indicate that the file-based path is being used.
- Telemetry should still accurately report that the native keychain is not available when the fallback is active.
- A dedicated setting should allow users to explicitly force the file-based storage path.
- The higher-level storage coordination logic should be simplified: instead of independently checking raw availability and managing a separate file storage class, it should ask the credential service whether the file-based fallback is currently active, and adjust the reported storage type accordingly.

## Why This Matters

Users running the CLI in environments without a native keychain (e.g., Docker containers, remote shells, CI pipelines) currently encounter hard failures when any credential operation is attempted. With the fallback in place, the CLI remains fully functional in these environments with credentials stored securely in an encrypted local file.
