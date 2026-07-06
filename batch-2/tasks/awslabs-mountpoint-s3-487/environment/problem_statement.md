## Description

The S3 filesystem implementation is too eager about reporting errors when files are opened. Currently, opening a file that is already being written to, has already been written, is stored in a low-priority retrieval class (such as Glacier), or is a locally-created file that hasn't yet been committed will immediately return an error when you try to open the file at all. This behavior is not consistent with how traditional POSIX filesystems work, where opening a file generally succeeds and errors are only reported when you actually try to read or write data.

## Expected Behavior

- Opening a file should succeed in all cases — whether the file is currently being written to, is already written, is in a restricted storage class, or is locally created.
- If a read or write operation on that file is not allowed, the error should be reported when the actual data transfer is attempted, not at open time.
- Files that are stored in a storage class that requires restoration should allow opening, but return an error when you try to read them.
- Files that are mid-write should allow opening, but return an error when another handle tries to read from them.
- Attempting to overwrite an already-written file without explicit truncation should fail at write time, not at open time.
- Additionally, a new configuration option should be introduced that, when enabled, allows existing files to be overwritten. When this option is enabled, a file can be opened with an explicit truncation request to clear its content and write new data. Without the truncation request, writes should still be rejected.

## Why This Matters

This change makes the mounted filesystem behave more predictably and consistently with standard filesystem semantics. Callers expect opening a file to succeed unless the file fundamentally doesn't exist or cannot be accessed at all, and they rely on read/write errors to understand what went wrong with actual data operations.
