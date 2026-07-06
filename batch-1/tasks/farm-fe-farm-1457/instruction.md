Implement a per-call options parameter for the module resolver to allow customized resolution behavior without altering global settings. Update the package metadata cache to incorporate these options into its cache key, ensuring distinct entries for different configurations.

*   Define and export a new struct `ResolveOptions` in the `farmfe_plugin_resolve::resolver` module.
    *   Implement the `Default` trait for `ResolveOptions` to allow construction with `ResolveOptions::default()`.
    *   Ensure `ResolveOptions` derives or implements `Debug`, `Clone`, `Hash`, `PartialEq`, `Eq`, and `Default`.
    *   Include a field `dynamic_extensions: Option<Vec<String>>` to store an optional list of file extensions for resolution.

*   Update the `Resolver::resolve()` method in `crates/plugin_resolve/src/resolver.rs`.
    *   Modify the method signature to include `options: &ResolveOptions` as the 4th parameter, following `kind: &ResolveKind`.
    *   Ensure all internal resolution logic propagates the `options` parameter through helper calls.

*   Modify the `PackageJsonLoader` struct in `crates/toolkit/src/resolve/package_json_loader.rs`.
    *   Implement a public method `get_cache_key(&self, path: &PathBuf, options: &Options) -> String`.
    *   Ensure the cache key is derived from both the path and options, encoding fields such as `follow_symlinks` and `resolve_ancestor_dir`.
    *   Use `get_cache_key()` for all cache insertions and lookups to maintain consistency.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.