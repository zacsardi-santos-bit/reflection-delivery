Implement a new lint rule called NoMisplacedAssertion to detect assertion calls placed outside proper test runner blocks. Ensure the rule flags assertions at the top level of a file or inside a test suite block but not within a test runner callback. Recognize common assertion patterns and provide clear diagnostics.

*   Implement the NoMisplacedAssertion lint rule:
    *   Flag assertion calls inside a `describe()` callback but not nested within `it()`, `test()`, or `Deno.test()` callbacks.
    *   Flag assertion calls at the top level of a file, outside any test runner callback.
    *   Recognize assertion functions: 'expect', 'assertEquals', and 'assert' (as a member expression like `assert.equal`).
    *   Flag unresolved global assertions if their names match recognized assertion function names.
    *   Check imported assertions against recognized specifiers: 'chai', 'node:assert', 'node:assert/strict', 'bun:test', 'vitest', or Deno assertion URLs.
        *   Flag misplaced assertions if the import source matches recognized specifiers.
        *   Do not flag assertions if the import source does not match any recognized specifier.
*   Provide diagnostic messages:
    *   Error message: 'The assertion isn't inside a it(), test() or Deno.test() function call.'
    *   First note: 'This will result in unexpected behaviours from your test suite.'
    *   Second note: 'Move the assertion inside a it(), test() or Deno.test() function call.'
    *   Underline the identifier of the assertion function call in diagnostics.
    *   Use diagnostic category 'lint/nursery/noMisplacedAssertion'.
*   Ensure valid usage does not produce diagnostics:
    *   Assertions inside `it()`, `test()`, or `Deno.test()` callbacks, even if nested inside `describe()`, should not be flagged.
    *   Recognize valid usage of Bun's test library and Deno's standard library assertions when used inside appropriate test functions.
*   Implement the rule in the specified locations:
    *   Rule: `crates/biome_js_analyze/src/lint/nursery/no_misplaced_assertion.rs`
        *   Implement the Rule trait and declare with `declare_rule!` macro.
    *   Module: `crates/biome_js_analyze/src/lint/nursery.rs`
        *   Declare `pub mod no_misplaced_assertion;` and register in `declare_group!`.
    *   DiagnosticCategory: `crates/biome_diagnostics_categories/src/categories.rs`
        *   Register category key "lint/nursery/noMisplacedAssertion".
    *   TypeAlias: `crates/biome_js_analyze/src/options.rs`
        *   Add type alias `pub type NoMisplacedAssertion`.
    *   StructField: `crates/biome_service/src/configuration/linter/rules.rs`
        *   Add `no_misplaced_assertion: Option<RuleConfiguration<NoMisplacedAssertion>>` to the Nursery struct.
        *   Register in the GROUP_RULES array, increasing its length from 23 to 24.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.