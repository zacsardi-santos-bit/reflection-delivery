Implement support for per-test annotations in the tree-sitter test corpus format to control test execution. Enhance the test runner to recognize and act on these annotations, allowing for more flexible and manageable test suites.

*   Add a new public struct `TestAttributes` in `cli/src/test.rs` with the following fields:
    *   `skip: bool`
    *   `platform: bool`
    *   `fail_fast: bool`
    *   `error: bool`
    *   `languages: Vec<Box<str>>`
    *   Derive `Debug`, `Clone`, `PartialEq`, and `Eq` for `TestAttributes`.
    *   Implement `Default` for `TestAttributes` with:
        *   `skip = false`
        *   `platform = true`
        *   `fail_fast = false`
        *   `error = false`
        *   `languages = vec!["".into()]` (a vector with one empty boxed string)

*   Update the `TestEntry::Example` variant in `cli/src/test.rs`:
    *   Add a new field `attributes: TestAttributes`.

*   Modify all construction sites of `TestEntry::Example` in `cli/src/test.rs` to include `attributes: TestAttributes::default()`.

*   Extend the `HEADER_REGEX` static in `cli/src/test.rs`:
    *   Include a named capture group `markers` to capture zero or more marker lines between the test name and the closing `===` delimiter.
    *   Marker lines start with `:` (e.g., `:skip`, `:platform(linux)`, `:fail-fast`, `:error`, `:language(foo)`).

*   Update `parse_test_content` function in `cli/src/test.rs`:
    *   Parse the `markers` capture group for each test header.
    *   Set `skip = true` for `:skip` marker.
    *   Set `fail_fast = true` for `:fail-fast` marker.
    *   Set `error = true` for `:error` marker.
    *   For `:platform(OS_NAME)` marker:
        *   Set `platform = true` if `OS_NAME` matches `std::env::consts::OS`.
        *   If at least one `:platform` marker is present and none match, set `platform = false`.
        *   Default `platform = true` if no `:platform` marker is present.
    *   For `:language(LANG)` marker:
        *   Append `LANG` (as `Box<str>`) to `languages`.
        *   Exclude the default empty-string entry if any `:language` marker is present.
    *   Use `TestAttributes::default()` when no markers are present.

*   Apply minor code style change in `cli/src/tests/parser_test.rs`:
    *   Replace `"\n".as_bytes()` with `b"\n"`.

*   Update the `syn` dependency version in `cli/src/tests/proc_macro/Cargo.toml` from `2.0.48` to `2.0.52`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.