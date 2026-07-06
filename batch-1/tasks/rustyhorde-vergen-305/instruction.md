Implement enhancements to the pretty-printing library for build metadata to ensure custom and non-standard metadata entries are preserved and displayed correctly. Update the library to handle keys that do not follow the standard naming convention and key paths that are shorter than expected.

*   Update the `split_key` function in `vergen-pretty/src/utils.rs`:
    *   Ensure it returns `Some(...)` for every input, including keys that do not contain 'vergen' (case-insensitive).
    *   For keys that do not contain 'vergen', return `Some` containing a single-element vector with the full lowercased key, paired with the original value string.

*   Update the `split_kv` function in `vergen-pretty/src/utils.rs`:
    *   Ensure it returns `Some(...)` for every input, including vectors with fewer than 2 elements.
    *   For vectors with fewer than 2 elements, return `Some` containing:
        *   The string "custom" as the category.
        *   The first element of the input vector as the label.
        *   The original value string.

*   Ensure display output works correctly for entries with the category 'custom':
    *   When such an entry has a value, the formatted display must be non-empty.

*   Extend the `vergen_pretty_env!` macro in `vergen-pretty/src/lib.rs`:
    *   Support an extended form that accepts one or more explicit environment variable name expressions.
    *   For each provided name, look up its compile-time value using `option_env!` and insert it into the resulting map alongside the standard vergen entries.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.