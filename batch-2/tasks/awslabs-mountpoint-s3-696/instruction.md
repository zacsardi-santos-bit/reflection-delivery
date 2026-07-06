Implement enhancements to the cloud filesystem tool to improve metadata caching behavior. Enforce a sensible maximum for metadata TTL values and introduce a negative caching capability to optimize file lookup efficiency.

*   Update the `--metadata-ttl` CLI argument:
    *   Reject values greater than 3,153,600,000 seconds (~100 years).
        *   Exit with a non-zero status and output: "'--metadata-ttl <SECONDS>': TTL must not be greater than 3153600000s (~100 years)".
    *   Reject values that cannot be parsed as a `u64` integer due to overflow.
        *   Exit with a non-zero status and output: "'--metadata-ttl <SECONDS>': number too large to fit in target type".

*   Modify the `CacheConfig` struct in `mountpoint-s3/src/fs.rs`:
    *   Add a field `negative_cache_size: usize` with a default value of 100,000.
    *   Ensure the struct implements the `Default` trait for compatibility with existing tests using `..Default::default()`.

*   Implement negative caching when the `negative_cache` feature is enabled:
    *   Store failed file lookups (file not found in S3) in the negative cache.
    *   Prevent subsequent lookups for the same path from triggering new S3 HEAD or LIST requests.
    *   Invalidate negative cache entries when a `readdirplus` or `readdir` operation discovers a previously missing file, allowing the next lookup to succeed without a new HEAD request.

*   Declare the `negative_cache` Cargo feature in `mountpoint-s3/Cargo.toml`:
    *   Ensure tests annotated with `#[cfg(feature = "negative_cache")]` are compiled and run when the feature is specified.

*   Implement the `parse_ttl_seconds` function in `mountpoint-s3/src/cli.rs`:
    *   Validate the provided seconds string as a `u64` and ensure it does not exceed 3,153,600,000 seconds.
    *   Return error messages for integer overflow and exceeding the maximum as specified.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.