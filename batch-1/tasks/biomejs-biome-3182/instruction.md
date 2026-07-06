Implement a new lint rule for Biome's nursery group that enforces consistent use of curly braces in JSX. Ensure the rule flags unnecessary curly braces around string values in JSX children and attribute values, and missing curly braces around JSX elements used as attribute values. Provide automatic fix suggestions for all violations.

*   Register the rule as 'lint/nursery/useConsistentCurlyBraces' in the Biome diagnostics category system.
    *   Add the category entry to `crates/biome_diagnostics_categories/src/categories.rs`.
*   Declare and register the rule in the nursery lint group module at `crates/biome_js_analyze/src/lint/nursery.rs`.
    *   Add `pub mod use_consistent_curly_braces;` to the module declarations.
    *   Include `self::use_consistent_curly_braces::UseConsistentCurlyBraces` inside the `declare_lint_group!` macro.
*   Implement the rule in `crates/biome_js_analyze/src/lint/nursery/use_consistent_curly_braces.rs` as a struct named `UseConsistentCurlyBraces`.
    *   Use the `declare_lint_rule!` macro with name 'useConsistentCurlyBraces', language 'jsx', recommended: false, and `fix_kind: FixKind::Unsafe`.
    *   Implement the `Rule` trait with `Query = Ast<AnyJsxCurlyQuery>` and `State = CurlyBraceResolution`.
*   Define the `CurlyBraceResolution` enum with variants `AddBraces` and `RemoveBraces`.
*   Declare `AnyJsxCurlyQuery` using `declare_node_union!` as `JsxAttributeInitializerClause | AnyJsxChild`.
*   Add a type alias in `crates/biome_js_analyze/src/options.rs`:
    *   `pub type UseConsistentCurlyBraces = <lint::nursery::use_consistent_curly_braces::UseConsistentCurlyBraces as biome_analyze::Rule>::Options;`
*   Add a configuration field in `crates/biome_configuration/src/linter/rules.rs`:
    *   `pub use_consistent_curly_braces: Option<RuleFixConfiguration<UseConsistentCurlyBraces>>`
    *   Insert "useConsistentCurlyBraces" into the `GROUP_RULES` array in alphabetical order.
*   Ensure the rule detects and emits errors for:
    *   String literals as JSX child expressions wrapped in curly braces.
        *   Emit: "Should not have curly braces around expression."
        *   Note: "JSX child does not need to be wrapped in curly braces."
    *   String literals as JSX attribute values wrapped in curly braces.
        *   Emit: "Should not have curly braces around expression."
        *   Note: "JSX attribute value does not need to be wrapped in curly braces."
    *   JSX elements used as attribute values without curly braces.
        *   Emit: "Should have curly braces around expression."
        *   Note: "JSX attribute value should be wrapped in curly braces. This will make the JSX attribute value more readable."
*   Provide 'Unsafe fix' suggestions:
    *   "Remove curly braces around the expression." for unnecessary braces.
    *   "Add curly braces around the expression." for missing braces.
    *   Convert string literals to plain JSX attributes or text.
    *   Preserve inline comments as separate JSX expression child nodes when removing braces.
*   Do not emit diagnostics for valid cases, such as plain text children, numeric or variable expressions, or correctly wrapped JSX elements.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.