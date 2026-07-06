## Description

When using Tauri's isolation security pattern, raw binary IPC requests are incorrectly deserialized. The isolation pattern works by having an isolated sandboxed page encrypt every IPC payload before it is forwarded to the Tauri backend. Each encrypted package includes metadata about the original payload, including its content type (whether it is binary data or structured data).

The current IPC request handler reads the content type from the outer HTTP headers only — but when the isolation pattern is used, the outer request always presents the same content type regardless of what the actual decrypted payload contains. As a result, raw binary payloads sent through the isolation pattern are misidentified and fail to deserialize correctly.

## Expected Behavior

- When the decrypted isolation payload carries a binary content indicator, the resulting IPC request body should be treated as raw binary data, not as structured data.
- When the decrypted isolation payload carries a structured-data content indicator, the resulting IPC request body should be parsed as structured data.
- The content type information embedded inside the encrypted payload must take precedence over the outer HTTP headers when the isolation pattern is active.

## Related Improvements

- The macro used to generate Tauri application context for tests should support a test-mode option so that platform-specific code generation (such as embedding macOS configuration metadata) is skipped during unit test compilation, preventing build failures on macOS.
- The IPC body type must support equality comparison in test contexts so that the correct deserialization can be verified in unit tests.
- The encrypted isolation payload structure must expose its embedded content type for use during decryption and body classification.

## Why This Matters

Users and developers relying on raw binary IPC communication (e.g., sending typed arrays or binary blobs from the frontend) through the isolation security pattern would see their data silently corrupted or their commands fail entirely.
