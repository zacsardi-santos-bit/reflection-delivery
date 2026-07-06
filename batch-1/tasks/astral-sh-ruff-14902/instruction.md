Extend the existing lint rule to detect lone-dot suffix arguments in path's suffix-replacement methods, in addition to dotless suffixes. Update the rule's name and behavior to reflect this broader scope.

*   Rename the PTH210 lint rule from `DotlessPathlibWithSuffix` to `InvalidPathlibWithSuffix`:
    *   Update the rule struct, check function, source file, and all references in `codes.rs`, `expression.rs`, and `flake8_use_pathlib/mod.rs`.
    *   Rename the source file from `dotless_pathlib_with_suffix.rs` to `invalid_pathlib_with_suffix.rs`.
    *   Update module declarations in `rules/mod.rs` to reflect the new file name.

*   Update the `InvalidPathlibWithSuffix` struct:
    *   Add a `single_dot: bool` field.
    *   Implement the `Violation` trait with `FIX_AVAILABILITY = FixAvailability::Sometimes`.
    *   Ensure the `message()` method returns:
        *   "Invalid suffix passed to `.with_suffix()`" when `single_dot` is true.
        *   "Dotless suffix passed to `.with_suffix()`" when `single_dot` is false.
    *   Ensure the `fix_title()` method returns:
        *   `Some("Remove \".\" or extend to valid suffix")` when `single_dot` is true, with no fix attached.
        *   `Some("Add a leading dot")` when `single_dot` is false, with an unsafe fix inserting a leading dot.

*   Implement the `invalid_pathlib_with_suffix` function:
    *   Detect when the suffix argument to a path `with_suffix()` call is exactly `"."` and flag it with `single_dot = true`.
    *   Ensure empty strings and strings starting with `'.'` but longer than one character are not flagged.

*   Update fixture files `PTH210.py` and `PTH210_1.py`:
    *   Include `path.with_suffix(".")` calls in the Errors section for all six path types (Path, PosixPath, PurePath, PurePosixPath, PureWindowsPath, WindowsPath).
    *   Ensure these calls are detected as errors by the rule.

*   Update snapshot files for PTH210.py and PTH210_1.py:
    *   Reflect new diagnostics for each lone-dot call, producing a PTH210 diagnostic without a fix marker (`[*]`).
    *   Include the message "Invalid suffix passed to `.with_suffix()`" and help text "Remove \".\" or extend to valid suffix".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.