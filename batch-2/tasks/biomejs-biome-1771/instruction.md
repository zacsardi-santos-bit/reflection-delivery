Implement a new lint rule named 'noConsole' to disallow any usage of the global console object in production code. Ensure the rule is comprehensive, covering all console method calls, and is aware of local variable shadowing. Provide an unsafe automatic fix to remove offending statements and ensure the rule is not enabled by default.

*   Implement the 'noConsole' rule in the nursery group:
    *   Create a struct named `NoConsole` in `crates/biome_js_analyze/src/semantic_analyzers/nursery/no_console.rs`.
    *   Use semantic analysis to distinguish between global console and local variables named "console".
    *   Flag any method call on the global console object, including indirect accesses via `globalThis.console`.
    *   Register the diagnostic category in `crates/biome_diagnostics_categories/src/categories.rs` as `"lint/nursery/noConsole": "https://biomejs.dev/linter/rules/no-console"`.
    *   Use the diagnostic title: "Don't use **console**".
    *   Use the diagnostic note: "Usage of **console** is disallowed."
    *   Provide an unsafe fix with the message "Remove console" that removes the entire call expression node.
    *   Declare the rule with `recommended: false` and `fix_kind: FixKind::Unsafe`.
    *   Register the rule in `crates/biome_js_analyze/src/semantic_analyzers/nursery.rs` with `pub(crate) mod no_console;` and include `self::no_console::NoConsole` in the `declare_group!` macro.

*   Update linter configuration:
    *   Add a `pub no_console: Option<RuleConfiguration>` field to the `Nursery` struct in `crates/biome_service/src/configuration/linter/rules.rs`.
    *   Add "noConsole" to the `GROUP_RULES` array in alphabetical order, before "noDuplicateJsonKeys".
    *   Update `GROUP_RULES` array size constant from 29 to 30.
    *   Update `ALL_RULES_AS_FILTERS` size from 29 to 30.
    *   Update `get_enabled_rules()`, `get_disabled_rules()`, and `get_rule_configuration()` methods to include the new `no_console` field at index 0.

*   Update TypeScript types:
    *   Add `noConsole?: RuleConfiguration` to the `Nursery` interface in `packages/@biomejs/backend-jsonrpc/src/workspace.ts`.
    *   Add "lint/nursery/noConsole" to the `Category` union type.

*   Update JSON schema:
    *   Add a "noConsole" property entry to the nursery rules section in `packages/@biomejs/biome/configuration_schema.json`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.