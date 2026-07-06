Fix the bug in the resource bundling system where mapped source files end up at incorrect locations. Ensure that the specified destination paths are used exactly as provided without appending the source file's name. Implement additional functionality to correctly handle directories, glob patterns, and non-existent source paths according to the specified requirements.

Requirements:

* Implement the `Resource` struct in `core/tauri-utils/src/resources.rs`:
    * Private fields: `path: PathBuf`, `target: PathBuf`.
    * Public accessor methods: `path(&self) -> &Path`, `target(&self) -> &Path`.
    * Implement `Debug` and `PartialEq` (in tests module).

* Implement the `ResourcePathsIter` struct in `core/tauri-utils/src/resources.rs`:
    * Implements `Iterator<Item = crate::Result<Resource>>`.
    * Implement `Debug`.
    * Method signature: `next(&mut self) -> Option<crate::Result<Resource>>`.

* Implement the `ResourcePaths` struct methods in `core/tauri-utils/src/resources.rs`:
    * `iter(&mut self) -> &mut ResourcePathsIter<'_>`: Returns a mutable reference to the internal iterator.
    * `new(patterns: &[String], allow_walk: bool) -> ResourcePaths<'_>`: Constructs from a slice of patterns.
    * `from_map(patterns: &std::collections::HashMap<String, String>, allow_walk: bool) -> ResourcePaths<'_>`: Constructs from a map of source to destination paths.

* Implement the `ResourcePathNotFound` error variant in `core/tauri-utils/src/lib.rs`:
    * Signature: `ResourcePathNotFound(std::path::PathBuf)`.

* Ensure correct behavior when iterating resources:
    * Slice mode with walk enabled:
        * Convert `..` path components to `_up_` segments.
        * Recursively walk directories and emit each file.
    * Slice mode with walk disabled:
        * Silently skip directories.
    * Map mode:
        * Use destination path as-is for files mapped to non-empty destinations.
        * Use filename only for files mapped to empty destinations.
        * Recursively walk directories when mapped to non-empty destinations, preserving structure.
        * Use filename only for glob patterns mapped to destinations.
        * Silently skip directories when walk is disabled.
        * Report non-existent source paths as `Err(crate::Error::ResourcePathNotFound(path))`.

* Add tests in `core/tauri-utils/src/resources.rs` within `#[cfg(test)] mod tests`:
    * Use `#[serial_test::serial]` attribute.
    * Use `getrandom` for randomized temporary directories.
    * Test functions:
        * `resource_paths_iter_slice_allow_walk`: Validates `ResourcePaths::new` with `allow_walk=true`.
        * `resource_paths_iter_slice_no_walk`: Validates `ResourcePaths::new` with `allow_walk=false`.
        * `resource_paths_iter_map_allow_walk`: Validates `ResourcePaths::from_map` with `allow_walk=true`.
        * `resource_paths_iter_map_no_walk`: Validates `ResourcePaths::from_map` with `allow_walk=false`.

* Update `core/tauri-utils/Cargo.toml` with dev-dependencies:
    * `getrandom = { version = "0.2", features = ["std"] }`
    * `serial_test = "3.1"`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.