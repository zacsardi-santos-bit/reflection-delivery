Implement security fixes for cargo's package unpacking process to address symlink attacks and decompression limits. Ensure the lock file is handled securely and enforce a maximum decompressed data size to prevent resource exhaustion.

*   Modify the package extraction process:
    *   Skip any tarball entry named `.cargo-ok` during extraction.
    *   Ensure `.cargo-ok` is created by cargo with an exclusive-create operation.
    *   Verify `.cargo-ok` is exactly 2 bytes long after a successful build.
    *   Prevent modification of symlink targets if `.cargo-ok` is a symlink.

*   Implement decompression limits:
    *   Enforce a maximum unpack size of 512 MB by default.
    *   Abort unpacking with exit code 101 if the limit is exceeded.
    *   Produce a specific error chain when the limit is exceeded:
        *   "failed to download replaced source registry `crates-io`"
        *   "failed to unpack package `<name> v<version> (registry `<registry>`)`"
        *   "failed to iterate over archive"
        *   "maximum limit reached when reading"

*   Implement `LimitErrorReader` in `src/cargo/util/io.rs`:
    *   Wrap any `Read` type with a byte limit.
    *   Return an `io::Error` with kind `Other` and message "maximum limit reached when reading" when the limit is reached.
    *   Constructor: `LimitErrorReader::new(r: R, limit: u64) -> LimitErrorReader<R>`
    *   Re-export as `pub(crate)` from `src/cargo/util/mod.rs`.

*   Define constants and functions:
    *   `MAX_UNPACK_SIZE: u64 = 512 * 1024 * 1024` in `src/cargo/sources/registry/mod.rs`.
    *   Function `max_unpack_size() -> u64` to return the effective maximum unpack size, considering the `__CARGO_TEST_MAX_UNPACK_SIZE` environment variable in debug builds.

*   Update test support:
    *   Add `symlink(dst: &str, src: &str) -> &mut Package` method in `crates/cargo-test-support/src/registry.rs` to add symlink entries to test packages.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.