Implement a new lint rule in the Biome JavaScript analyzer to enforce using modern, standardized string trimming methods over deprecated aliases. Detect and report method calls using deprecated aliases, and provide an automated fix to replace them with the standardized equivalents.

*   Implement a lint rule named `UseTrimStartEnd` in `crates/biome_js_analyze/src/lint/nursery/use_trim_start_end.rs`.
    *   Declare using `declare_lint_rule!` with `name: "useTrimStartEnd"` and `fix_kind: FixKind::Safe`.
    *   Query on `JsCallExpression` nodes.
*   Ensure the rule flags:
    *   Call expressions with method name `trimLeft` and zero arguments, reporting 'Use trimStart instead of trimLeft.' with an info note 'trimLeft is an alias for trimStart.'
    *   Call expressions with method name `trimRight` and zero arguments, reporting 'Use trimEnd instead of trimRight.' with an info note 'trimRight is an alias for trimEnd.'
    *   Dot-notation and bracket-notation calls with string literal keys in single quotes, double quotes, or template literals.
*   Ensure the rule does NOT flag:
    *   Computed bracket notation with variable keys (e.g., `foo[trimLeft]()`).
    *   Calls with one or more arguments.
    *   `NewExpression` nodes.
    *   Cases where `trimLeft` or `trimRight` is the object of a member expression.
    *   Standalone identifier calls without a receiver object.
    *   Cases where `trimLeft` or `trimRight` appears as an argument.
*   Provide a safe auto-fix:
    *   For dot-notation, replace the identifier token with 'Replace trimLeft with trimStart.' or 'Replace trimRight with trimEnd.'
    *   For bracket notation:
        *   Single-quote string: 'Replace trimLeft with \'trimStart\'.'
        *   Double-quote string: 'Replace trimLeft with "trimStart".'
        *   Template literal: 'Replace trimLeft with trimStart.'
    *   Preserve surrounding code, including inline comments and whitespace.
*   Register the rule under the category `lint/nursery/useTrimStartEnd`.
    *   Include in the nursery rule group and declare as a safe-fix rule.
*   In `crates/biome_js_analyze/src/lint/nursery.rs`, declare `pub mod use_trim_start_end;` and include `UseTrimStartEnd` in the `declare_lint_group!` macro.
*   Register the diagnostic category in `crates/biome_diagnostics_categories/src/categories.rs` with `"lint/nursery/useTrimStartEnd"` mapping to `"https://biomejs.dev/linter/rules/use-trim-start-end"`.
*   In `crates/biome_configuration/src/linter/rules.rs`, add `pub use_trim_start_end: Option<RuleFixConfiguration<UseTrimStartEnd>>` to the `Nursery` struct.
    *   Add `"useTrimStartEnd"` to `GROUP_RULES`.
    *   Include in `get_enabled_rules` and `get_disabled_rules` dispatch logic.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.