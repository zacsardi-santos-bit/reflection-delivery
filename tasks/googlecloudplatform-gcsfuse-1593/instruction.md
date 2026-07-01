Implement separate handling of stat-cache and type-cache TTL settings in the GCS FUSE tool, ensuring they are stored independently and not merged at startup. Centralize the TTL computation logic into a reusable function and validate configuration-file TTL values against an upper bound to prevent overflow errors.

*   Update `flagStorage` struct in `flags.go`:
    *   Replace the combined TTL field with `StatCacheTTL` and `TypeCacheTTL`, both of type `time.Duration`.
    *   Remove the old combined field.
    *   Default both new fields to `mount.DefaultStatOrTypeCacheTTL`.
    *   Ensure JSON serialization uses keys "StatCacheTTL" and "TypeCacheTTL".

*   Define constants:
    *   `DefaultStatCacheCapacity` in `flags.go` with value 4096.
    *   `DefaultStatOrTypeCacheTTL` in `internal/mount/flag.go` as `time.Duration = time.Minute`.
    *   `MaxSupportedTtlInSeconds` in `internal/config/mount_config.go` with value `int64(math.MaxInt64 / int64(time.Second))`, which equals 9223372036.
    *   `MetadataCacheTtlSecsTooHighError` in `internal/config/yaml_parser.go` with value: "the value of ttl-secs in metadata-cache is too high to be supported. Max is 9223372036."

*   Implement `MetadataCacheTTL` function in `internal/mount/flag.go`:
    *   Signature: `MetadataCacheTTL(statCacheTTL, typeCacheTTL time.Duration, ttlInSeconds int64) time.Duration`.
    *   Return `time.Duration(math.MaxInt64)` if `ttlInSeconds` is -1.
    *   Return `time.Second * time.Duration(ttlInSeconds)` if `ttlInSeconds` is 0 or a positive number.
    *   If `ttlInSeconds` equals `config.TtlInSecsUnsetSentinel`, use old-flag logic: return 0 if either `statCacheTTL` or `typeCacheTTL` is 0; otherwise, return the minimum of the two values rounded up to the nearest second.

*   Validate configuration-file TTL:
    *   In `ParseConfigFile` (in `internal/config`), ensure the `metadata-cache ttl-secs` value does not exceed `MaxSupportedTtlInSeconds`.
    *   Return an error with `MetadataCacheTtlSecsTooHighError` message if the value is too high.

*   Create test data file `internal/config/testdata/metadata_cache_config_ttl_too_high.yaml`:
    *   Include a `metadata-cache` configuration with `ttl-secs` value of 9223372037 to trigger the too-high validation error.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.