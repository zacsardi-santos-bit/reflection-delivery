## Description

There is a bug in the WASI filesystem implementation where file truncation is not correctly blocked when a guest WebAssembly module opens a file that has been preopened with read-only file access. When a host application grants a guest only read permissions on files in a preopened directory, the guest should not be able to truncate (resize to zero or smaller) those files. Currently, truncation can succeed even when write permissions were never granted.

## Expected Behavior

- When a directory is preopened with read-only file permissions, any attempt by a guest module to truncate files in that directory should be rejected by the runtime.
- After a guest module attempts and fails to truncate a read-only file, the file's original contents must remain completely intact on the host.
- The guest module should receive an appropriate error from the failed truncation attempt, rather than silently succeeding or triggering an unrecoverable trap.
- This protection should apply to both the legacy module-based and newer component-based WASI interfaces.

## Why This Matters

Host applications that expose read-only views of directories to guest WebAssembly modules rely on the WASI runtime to enforce those access restrictions. If truncation bypasses permission checks, a sandboxed guest could corrupt files that the host intended to be immutable. Fixing this ensures that read-only file permissions are actually enforced, making the WASI sandbox boundary reliable for filesystem access control.
