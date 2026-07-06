Implement a new lint rule in Biome's nursery category to detect and warn against using await expressions inside loop constructs, which can lead to unintended sequential execution and performance issues. Ensure the rule is smart about scoping and only flags relevant cases.

*   Implement the lint rule in the file `crates/biome_js_analyze/src/lint/nursery/no_await_in_loop.rs`:
    *   Use the `declare_lint_rule!` macro with the name "noAwaitInLoop" under the "nursery" group.
    *   Ensure the rule flags await expressions and for-await-of statements in loop bodies or conditions.
    *   Provide a diagnostic error message: "Avoid using await inside loops."
    *   Include an informational hint: "Using await inside loops might cause performance issues or unintended sequential execution, consider use Promise.all() instead."
    *   Ensure the rule does not flag awaits in nested functions, iterable positions, or top-level for-await-of loops.

*   Register the lint rule in the nursery module:
    *   Add `pub mod no_await_in_loop;` to `crates/biome_js_analyze/src/lint/nursery.rs`, alphabetically before `no_common_js`.
    *   Add `self::no_await_in_loop::NoAwaitInLoop` to the `declare_lint_group!` macro for `Nursery`.

*   Register the diagnostic category:
    *   Add `"lint/nursery/noAwaitInLoop": "https://biomejs.dev/linter/rules/no-await-in-loop"` in alphabetical order in `crates/biome_diagnostics_categories/src/categories.rs`.

*   Update options and configuration:
    *   Add `pub type NoAwaitInLoop = <lint::nursery::no_await_in_loop::NoAwaitInLoop as biome_analyze::Rule>::Options;` in `crates/biome_js_analyze/src/options.rs`.
    *   Add `pub no_await_in_loop: Option<RuleConfiguration<biome_js_analyze::options::NoAwaitInLoop>>` to the `Nursery` struct in `crates/biome_configuration/src/analyzer/linter/rules.rs`, alphabetically before `no_common_js`.
    *   Add `"noAwaitInLoop"` to the `GROUP_RULES` array as the first entry.
    *   Update all index-based configurations (`RECOMMENDED_RULES_AS_FILTERS`, `ALL_RULES_AS_FILTERS`, `get_enabled_rules`, `get_disabled_rules`) to account for the new first entry.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.