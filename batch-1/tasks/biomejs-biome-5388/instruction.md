Implement a new lint rule named 'noPrivateImports' in the 'correctness' category to enforce symbol-level visibility annotations in JavaScript and TypeScript projects. This rule should read JSDoc annotations to determine the visibility of exported symbols and raise diagnostics when an import violates the declared visibility. 

*   Create the module `no_private_imports` in `crates/biome_js_analyze/src/lint/correctness/no_private_imports.rs`.
    *   Implement the `NoPrivateImports` struct using the `declare_lint_rule!` macro in the correctness group.
    *   Define the `NoPrivateImportsOptions` struct with a `default_visibility` field of type `Visibility`.
    *   Define the `Visibility` enum with variants `Public`, `Package`, and `Private`.
    *   Implement the `NoPrivateImportsState` struct to capture import violations.
*   Ensure the rule:
    *   Reads visibility annotations: '@private', '@access private', '@package', '@public', '@access public'.
    *   Accepts a `defaultVisibility` configuration option, defaulting to `public`.
    *   Flags imports of private symbols with the message: "You may not import a symbol with private visibility from here."
    *   Flags imports of package-private symbols from outside their directory with the message: "You may not import a symbol with package visibility from here."
    *   Allows imports of public symbols without diagnostics.
    *   Allows imports of package-private symbols within the same directory.
    *   Allows re-exported package-private symbols to be imported via the index file path.
    *   Allows private symbols to be imported from the same folder's index file.
    *   Flags default and combined imports of private symbols.
    *   Prioritizes explicit annotations over the `defaultVisibility` setting.
*   Update the `Correctness` configuration struct in `crates/biome_configuration/src/analyzer/linter/rules.rs` to include `no_private_imports`.
*   Add the rule name "noPrivateImports" to the GROUP_RULES array alphabetically.
*   Modify the `analyze_and_snap` function in `crates/biome_js_analyze/tests/spec_tests.rs` to:
    *   Enable dependency graph construction for "noPrivateImports".
    *   Normalize Windows path separators in snapshots.
*   Remove the old nursery rule `noPackagePrivateImports` and its test fixtures.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.