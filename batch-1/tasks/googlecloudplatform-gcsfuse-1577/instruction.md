Implement a configurable maximum entry count for the type cache in the gcsfuse codebase. Move filesystem entry type constants to a shared metadata package and add new types for symbolic links and absent entries. Ensure the type cache respects configuration settings and update it consistently after directory operations.

* Implement the TypeCache interface in `internal/cache/metadata` with methods:
    * `Get(now time.Time, name string) Type`
    * `Insert(now time.Time, name string, t Type)`
    * `Erase(name string)`

* Implement `NewTypeCache(maxEntries int, ttl time.Duration) TypeCache` in `internal/cache/metadata`:
    * Return a `*typeCache` struct.
    * Disable cache if `maxEntries` is 0 or `ttl` is 0.
    * Allow unlimited entries if `maxEntries` is negative.
    * Evict oldest entry if `maxEntries` is positive and capacity is exceeded.

* Define `Type` as an int in `internal/cache/metadata` with constants:
    * `UnknownType`, `RegularFileType`, `ExplicitDirType`, `ImplicitDirType`, `NonexistentType`, `SymlinkType`.

* Update `MetadataCacheConfig` in `internal/config`:
    * Add `TypeCacheMaxEntries` field with YAML tag `type-cache-max-entries` and JSON key `TypeCacheMaxEntries`.
    * Default to `DefaultTypeCacheMaxEntries` if unspecified.

* Define constants in `internal/config`:
    * `TypeCacheMaxEntriesInvalidValueError`: "the value of type-cache-max-entries for metadata-cache can't be less than -1".
    * `DefaultTypeCacheMaxEntries`: 2 << 20 (2097152).

* Update `NewDirInode` and `NewExplicitDirInode` in `internal/fs/inode` to accept `typeCacheMaxEntries` parameter.

* Ensure type cache updates:
    * After `LookUpChild`, `ReadEntries`, `CreateChildFile`, `CreateChildSymlink`, `CreateChildDir`, `CloneToChildFile`, and `DeleteChildFile`.

* Add and update test data files in `internal/config/testdata/`:
    * `metadata_cache_config_invalid_type-cache-max-entries.yaml` with `type-cache-max-entries: -2`.
    * `metadata_cache_config_type-cache-max-entries_unset.yaml` without `type-cache-max-entries`.
    * Update `valid_config.yaml` with `type-cache-max-entries: 1`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.