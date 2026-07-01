Update the disk usage tool to handle paths correctly across different operating systems by implementing a new path normalization function and updating existing functions to use platform-aware path utilities. Ensure paths are correctly deduplicated and parent-child relationships are accurately determined.

*   Implement a new public function `normalize_path` in `src/utils/mod.rs`:
    *   Accept a path of type `P: AsRef<std::path::Path>`.
    *   Return a normalized `String` using OS-native path utilities.
    *   Remove repeated separators, interior current-directory segments, and trailing separator/dot combinations.
    *   Ensure paths like 'a/b', 'a/b//', and 'a/././b///' normalize to the same value.

*   Update the `is_a_parent_of` function in `src/utils/mod.rs`:
    *   Use platform-aware path comparison instead of string prefix matching.
    *   Return `false` when parent and child are the same directory, including variations with trailing slashes or dot components.
    *   Return `false` for paths that share a string prefix but are not actual directory ancestors.
    *   Ensure it returns `false` when both arguments are '/'.

*   Modify the `simplify_dir_names` function in `src/utils/mod.rs`:
    *   Use `normalize_path` instead of `strip_end_slash` for path normalization.
    *   Ensure paths equivalent under normalization are correctly deduplicated.

*   Update the `format_string` function in `src/display.rs`:
    *   Split path names using an OS-aware separator check.
    *   Support both '/' and platform-native separators for computing short display names.

*   Revise the `trim_deep_ones` function in `src/utils/mod.rs`:
    *   Count path depth using an OS-aware separator check rather than only counting '/' characters.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.