Implement enhancements to the `ExternalSpillableMap` class to ensure proper resource management and correct behavior when the map is empty. Ensure that the map can be used safely in try-with-resources blocks and that all standard map operations work correctly on an empty map without throwing exceptions.

*   Implement `java.io.Closeable` in `ExternalSpillableMap`:
    *   Add a `close()` method to release all resources, including clearing in-memory data and closing any initialized disk-based storage.
    *   Ensure `ExternalSpillableMap` can be used in try-with-resources blocks for automatic resource management.

*   Ensure correct behavior on an empty map:
    *   `isEmpty()` must return `true` and `size()` must return `0` without exceptions when the map is newly created and contains no entries.
    *   `containsKey(key)` and `containsValue(value)` must return `false` without exceptions when the map is empty.
    *   `keySet().isEmpty()`, `values().isEmpty()`, and `entrySet().isEmpty()` must return `true` without exceptions when the map is empty.
    *   `valueStream().count()` must return `0` and `iterator().hasNext()` must return `false` without exceptions when the map is empty.

*   Support all disk storage types:
    *   Ensure all empty-map operations work correctly across all disk storage types available in `ExternalSpillableMap.DiskMapType`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.