Implement changes to the S3 filesystem to defer error reporting from the open operation to the read and write operations, aligning behavior with POSIX-style filesystems. Introduce a configuration option to allow file overwriting when explicitly requested.

*   Update the `open` method in `mountpoint-s3/src/fs.rs`:
    *   Ensure it always returns a valid file handle, regardless of file state.
    *   Defer errors to the `read` or `write` operations.

*   Modify the `write` method in `mountpoint-s3/src/fs.rs`:
    *   Return EPERM when writing to an already-uploaded file opened without O_TRUNC.
    *   Return EPERM when writing from a second handle while the file is actively being written.
    *   Return EPERM when writing to a file opened without O_TRUNC when `allow_overwrite` is enabled.
    *   Return EPERM when writing from a handle while another handle is reading the same file.

*   Adjust the `read` method in `mountpoint-s3/src/fs.rs`:
    *   Return EACCES for files in flexible/Glacier-retrieval storage class.
    *   Return EPERM when reading a locally-created (mid-write) file from a separate read handle.
    *   Return EBADF when reading from a write-only file handle.
    *   Return EBADF when reading from a read-write handle while the same handle is mid-write.

*   Implement the `allow_overwrite` field in the `S3FilesystemConfig` struct:
    *   Default to false.
    *   Enable overwrite mode when set to true, allowing files to be opened with O_TRUNC for writing.

*   Handle specific scenarios:
    *   Fail with EINVAL when attempting to open an existing file with the O_APPEND flag.
    *   Allow opening an existing file for writing without O_APPEND and without O_TRUNC, but fail subsequent writes with EPERM.
    *   When `allow_overwrite` is enabled and a file is opened with O_TRUNC, truncate the file immediately.
    *   When `allow_overwrite` is enabled, allow writing to a file opened with O_TRUNC, ensuring the file contains the written content after closure.
    *   When `allow_overwrite` is enabled, fail writes with EPERM if the file was opened without O_TRUNC.
    *   When `allow_overwrite` is enabled, fail writes from a handle if another handle has already read data.
    *   When `allow_overwrite` is enabled, fail writes with EBADF if a read-write handle has already performed a read.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.