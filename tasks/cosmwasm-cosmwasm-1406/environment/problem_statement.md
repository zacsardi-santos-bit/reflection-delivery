## Description

When the VM attempts to open a WebAssembly file that does not exist, the error it returns contains the operating system's own description of what went wrong. On Linux this might say something like "no such file or directory," while on Windows it says something completely different. This means the exact error text varies by platform, which makes the test suite impossible to run reliably on Windows and breaks any logic that checks error messages for diagnostic or consensus purposes.

## Expected Behavior

- When a WASM file cannot be opened for reading (e.g., because it does not exist), the error message must be a fixed, platform-independent string — something that communicates the failure without embedding OS-specific detail.
- Likewise, any other file-system errors in the cache layer (failed reads, failed directory creation) should similarly avoid leaking OS-specific text.
- Any existing code that previously matched against the old OS-specific error format must be updated to use the new standardized message.

## Why This Matters

The project is working toward being able to build and test the VM on Windows. OS-specific error messages in the cache layer are a blocker: they appear in test assertions and potentially in consensus-critical error paths. Making these messages deterministic removes a whole class of cross-platform failures and brings the Windows build closer to parity with Linux.
