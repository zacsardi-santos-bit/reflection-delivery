## Description

There is a security vulnerability in the WASI filesystem host implementation: a WebAssembly guest program can bypass the host's write-restriction policy by using the file truncation flag at open time.

When a host registers a preopened directory where files are only permitted to be read (no write access granted), the expectation is that guest programs cannot modify those files in any way. However, by requesting truncation when opening a file, a guest can empty the contents of a read-only file without ever needing explicit write permission. The host fails to recognize truncation as a write operation for the purposes of its permission check.

## Expected Behavior

- When a preopened directory is configured with read-only file access (no write permission), any attempt by a guest WASM program to open a file with the truncation flag should be rejected with a permission denied error.
- After such a rejected attempt, the file contents must remain exactly as they were — no data should be erased or modified.
- This behavior must hold for both the WASI Preview 1 (syscall-based) and WASI Preview 2 (component model) interfaces.

## Why This Matters

Hosts use preopened directory permissions to sandbox WebAssembly programs and enforce least-privilege access to the filesystem. If the truncation path bypasses write-permission checks, the host's security boundary is incomplete, and guest programs can silently destroy file data in directories that were supposed to be read-only. This is a correctness and security fix.
