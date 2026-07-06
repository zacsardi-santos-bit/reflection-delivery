## Description

The MongoDB Go driver currently fails to handle CA certificate files that contain multiple certificates (such as files with intermediate certificates in a chain). When a connection URI specifies a CA file with multiple PEM certificates, only the first certificate is loaded into the trusted certificate pool. This can silently cause TLS validation failures in environments that use certificate chains.

Additionally, error messages when a CA file is invalid are inconsistent and not user-friendly. The driver currently returns different internal error messages depending on whether the file is empty, contains no certificate blocks, or has malformed content — none of which clearly describe what went wrong.

## Expected Behavior

- When a CA file containing multiple PEM certificates is specified, all certificates must be loaded into the trusted pool.
- When the CA file is empty, contains no certificate blocks (e.g., only a private key), or contains malformed data, the driver should report a consistent, clear error indicating that no valid certificates were found in the file.

## Why This Matters

Users who configure TLS with certificate chains (a common pattern in enterprise environments) need all certificates in the chain to be trusted. Additionally, clearer error messages help developers quickly diagnose misconfigured CA files rather than having to trace through cryptic internal errors.
