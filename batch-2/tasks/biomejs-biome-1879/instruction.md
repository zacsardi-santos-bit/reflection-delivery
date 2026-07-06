Implement a new lint rule named 'noExportsInTest' for the Biome JavaScript linter. This rule should detect and flag export statements in test files to prevent unintended duplicate test executions. Ensure the rule is categorized under the nursery group and is marked as recommended.

*   Implement the 'noExportsInTest' lint rule:
    *   Create the rule in `crates/biome_js_analyze/src/analyzers/nursery/no_exports_in_test.rs`.
    *   Use the `declare_rule!` macro with the name "noExportsInTest", version "next", and recommended: true.
    *   Detect test files by identifying test framework function calls such as `describe`, `expect`, `it`, or `test`.
    *   Flag ES module export statements in test files:
        *   Include both named exports (e.g., `export const`) and default exports (e.g., `export default`).
    *   Flag CommonJS export patterns in test files:
        *   Direct assignments to `module.exports` (e.g., `module.exports = ...`).
        *   Computed-bracket property assignments (e.g., `module.exports["key"] = ...`).
        *   Dot-notation property assignments (e.g., `module.exports.key = ...`).
    *   Ensure each flagged export produces a diagnostic covering the full range of the statement or assignment.
    *   Use the diagnostic message: "Do not export from a test file."
    *   Set the diagnostic category to 'lint/nursery/noExportsInTest'.

*   Ensure correct behavior:
    *   Do not produce diagnostics for files with exports but without test framework calls.
    *   Do not trigger the rule for assignments to properties other than the CommonJS exports object, even in test files.

*   Register the rule module:
    *   Add `pub mod no_exports_in_test;` to `crates/biome_js_analyze/src/analyzers/nursery.rs`.
    *   Include `no_exports_in_test::NoExportsInTest` in the `declare_group!` macro.

*   Register the diagnostic category:
    *   Add an entry to `crates/biome_diagnostics_categories/src/categories.rs` using the `define_categories!` macro.
    *   Use the key "lint/nursery/noExportsInTest" with the URL "https://biomejs.dev/linter/rules/no-exports-in-test".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.