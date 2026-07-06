Implement a new CSS lint rule to detect when a declared CSS custom property is used without the `var()` function. Ensure the rule is scope-aware and only flags cases where the property is declared within accessible scope. Additionally, update the existing rule for detecting undeclared dependencies to be part of the stable correctness category.

*   Implement the `NoMissingVarFunction` lint rule in the nursery group at `crates/biome_css_analyze/src/lint/nursery/no_missing_var_function.rs`.
    *   Register this rule in the nursery lint group module at `crates/biome_css_analyze/src/lint/nursery.rs` using the `declare_lint_group!` macro.
    *   Ensure the rule uses the biome CSS semantic model (`Semantic<CssDashedIdentifier>`) to access scoped custom property declarations.
    *   Emit an error diagnostic when a CSS custom property is used without the `var()` function, if declared in accessible scope.
        *   Error message: "CSS variables '<variable_name>' is used without the 'var()' function".
        *   Info/note message: "CSS variables should be used with the 'var()' function to ensure proper fallback behavior and browser compatibility."
    *   Check scope hierarchically: current block, ancestor selector blocks, and global scope.
    *   Do not emit diagnostics for undeclared custom properties or when already wrapped in `var()`.
    *   Exclude diagnostics for certain CSS properties like transition, animation, grid, counter, and view transition properties.
    *   Handle nested CSS selectors correctly: properties declared in a parent are accessible in child blocks, but not vice versa.
    *   Add a new diagnostic category entry: `"lint/nursery/noMissingVarFunction": "https://biomejs.dev/linter/rules/no-missing-var-function"`.

*   Update the `noUndeclaredDependencies` lint rule:
    *   Register it under the 'correctness' rule group.
    *   Ensure the termination message category is 'lint' with the message: 'Some errors were emitted while running checks.'
    *   Set the diagnostic path to `lint/correctness/noUndeclaredDependencies`.

*   Update configuration and options:
    *   Add a type alias for the rule's options in `crates/biome_css_analyze/src/options.rs`: `pub type NoMissingVarFunction = <lint::nursery::no_missing_var_function::NoMissingVarFunction as biome_analyze::Rule>::Options;`.
    *   Add a field `no_missing_var_function` to the Nursery configuration struct in `crates/biome_configuration/src/analyzer/linter/rules.rs`.
    *   Include "noMissingVarFunction" in the `GROUP_RULES` and `GROUP_RECOMMENDED` arrays in alphabetical order.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.