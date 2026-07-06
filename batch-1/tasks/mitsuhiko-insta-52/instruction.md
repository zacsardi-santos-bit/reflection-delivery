Implement a feature to replace the special vertical ellipsis character (⋮) used for inline snapshot indentation with regular whitespace, ensuring compatibility with existing snapshots and consistent behavior across all snapshot types.

*   Ensure the system recognizes and processes legacy snapshots:
    *   Detect snapshots starting with the ⋮ character and strip this prefix from each line to recover the content.
    *   Maintain backward compatibility with existing snapshot files using the ⋮ format.
*   Normalize snapshots using plain whitespace indentation:
    *   Compute and strip the minimum common leading whitespace from all non-empty lines.
    *   Skip leading empty lines when calculating indentation.
    *   Trim trailing whitespace from the snapshot content.
*   Update the system to write or update inline snapshots using space indentation:
    *   Align the indentation with the surrounding code indentation level.
    *   Avoid using the ⋮ character prefix in new or updated snapshots.
*   Ensure all snapshot assertion types (debug, YAML, RON, YAML with redactions) function correctly with the new format.
*   Confirm the library is compatible with Rust 2018 edition or later without requiring an explicit extern crate declaration.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.