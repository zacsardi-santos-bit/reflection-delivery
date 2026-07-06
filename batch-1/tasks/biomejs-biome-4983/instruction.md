Implement a new lint rule named 'noDestructuredProps' for Solid.js projects in the nursery group to detect and warn against destructuring props directly in component function parameters. This rule should ensure that developers use property accesses on the props object to preserve reactivity.

*   Implement the 'noDestructuredProps' lint rule:
    *   Create a Rust struct named 'NoDestructuredProps' in `crates/biome_js_analyze/src/lint/nursery/no_destructured_props.rs`.
    *   Use the `declare_lint_rule!` macro with name "noDestructuredProps", language "js", and recommended: false.
    *   Register the rule in `crates/biome_js_analyze/src/lint/nursery.rs` by adding `pub mod no_destructured_props;` and including `self::no_destructured_props::NoDestructuredProps` in the `declare_lint_group!` macro.

*   Register the diagnostic category:
    *   Add the category string "lint/nursery/noDestructuredProps" in `crates/biome_diagnostics_categories/src/categories.rs` with a link to "https://biomejs.dev/linter/rules/no-destructured-props".

*   Define the options type alias:
    *   Export a public type alias `NoDestructuredProps` in `crates/biome_js_analyze/src/options.rs` as the Rule::Options type for the rule.

*   Update the configuration:
    *   Add a field `no_destructured_props` of type `Option<RuleConfiguration<biome_js_analyze::options::NoDestructuredProps>>` to the `Nursery` struct in `crates/biome_configuration/src/analyzer/linter/rules.rs`.
    *   Include a doc comment "Disallow destructuring props inside JSX components in Solid projects." and use `#[serde(skip_serializing_if = "Option::is_none")]`.
    *   Add "noDestructuredProps" to the GROUP_RULES array in alphabetical order.

*   Ensure the rule flags:
    *   Arrow functions with a single destructured parameter assigned to a PascalCase variable.
    *   All destructuring forms: simple properties, aliased properties, computed keys, default values, rest elements, and TypeScript-typed destructuring.
    *   Empty destructuring patterns with the message: "You cannot destructure props."
    *   Each destructured variable used in JSX with the message: "This variable shouldn't be destructured." and a secondary detail pointing to the destructuring site.

*   Ensure the rule does not flag:
    *   Functions with more than one parameter.
    *   Functions using plain props objects.
    *   Destructuring inside the function body or nested inner functions.
    *   Bare JSX element declarations.

*   Include exact diagnostic messages:
    *   Primary message for empty destructuring: "You cannot destructure props."
    *   Primary message for variable usage: "This variable shouldn't be destructured."
    *   Secondary detail: "This is where the props were destructured."
    *   Info note 1: "In Solid, props must be used with property accesses (props.foo) to preserve reactivity."
    *   Info note 2: "Remove the destructuring and use props.foo instead."

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.