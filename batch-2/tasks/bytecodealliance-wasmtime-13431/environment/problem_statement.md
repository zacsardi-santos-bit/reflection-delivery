## Description

There is a security bug in the WASI filesystem implementation: when a WebAssembly guest opens a file using the truncation open flag, the host does not check whether the guest actually has write permission before allowing the operation. This means an operator who configures a preopened directory with read-only file access cannot trust that restriction — a guest program can silently destroy file contents by requesting truncation even when only granted read access.

## Expected Behavior

- When the host configures a preopened directory with read-only file permissions (no write), a guest that opens a file requesting truncation should receive a "not permitted" error.
- The file's contents should remain completely unchanged after the rejected truncation attempt.
- The same enforcement should apply under both WASI Preview 1 and WASI Preview 2 APIs.

## Why This Matters

Host operators use file permission settings to enforce security boundaries. If those restrictions can be bypassed simply by setting a truncation flag, data loss can occur within ostensibly read-protected directories. This is a correctness and security issue that breaks the fundamental guarantee that read-only preopened files cannot be modified by a guest.
