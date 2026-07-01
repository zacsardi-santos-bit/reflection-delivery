Implement a native suppression comment format for the Ruff type checker that allows users to suppress type-checking errors with a Ruff-specific comment style. Ensure the new format can suppress all errors or specific diagnostic codes on a line, and fix the existing issue where suppression comments are not recognized after another inline comment directive on a continuation line.

*   Update the `Db` trait in `crates/red_knot_python_semantic/src/db.rs`:
    *   Add a new required method `fn lint_registry(&self) -> &LintRegistry` to return a reference to the lint registry.

*   Modify the `default_lint_registry` function in `crates/red_knot_python_semantic/src/lib.rs`:
    *   Change the return type to `&'static LintRegistry`.
    *   Ensure the registry is initialized lazily on the first call.

*   Implement the `lint_registry` method in the test `Db` struct in `crates/red_knot_test/src/db.rs`:
    *   Return `default_lint_registry()`.
    *   Import `LintRegistry` from `red_knot_python_semantic::lint`.
    *   Update `RuleSelection::from_registry` to use `default_lint_registry()` directly.

*   Create a new markdown test file at `crates/red_knot_python_semantic/resources/mdtest/suppressions/knot-ignore.md`:
    *   Define test cases for the `knot: ignore` suppression comment.

*   Implement the `knot: ignore` comment functionality:
    *   `# knot: ignore` suppresses all type-checking errors on a line.
    *   `# knot: ignore[code]` suppresses only the specified diagnostic code.
    *   `# knot: ignore[code1, code2]` suppresses multiple specified codes.
    *   Ensure syntax errors and `revealed-type` diagnostics are not suppressible.
    *   Allow extra whitespace around the colon and within the code list.
    *   Allow optional whitespace between `#` and `knot`.
    *   Permit a trailing comma after the last code in the list.
    *   Ensure an empty code list suppresses nothing.
    *   Reject invalid characters in code names, leaving errors visible.
    *   Require a comma separator between codes; missing commas invalidate suppression.
    *   Accept trailing whitespace after the code list.

*   Fix the issue where suppression comments are not recognized after another inline comment directive on a continuation line.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.