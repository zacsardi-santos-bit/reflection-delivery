I'm using the unused declarations lint rule in my project and I'd like more control over how it applies auto-fixes.

*   The no_unused_vars rule must accept a new top-level 'fix' configuration object with two sub-options: 'imports' and 'variables', each accepting the string values 'off', 'suggestion', or 'fix'.

*   When 'fix.imports' is set to 'off', unused import violations must be reported as diagnostics but with no fix or suggestion attached (plain diagnostic only).

*   When 'fix.variables' is set to 'off', unused variable violations (including catch bindings) must be reported as diagnostics but with no fix or suggestion attached.

*   Setting 'fix.imports' to 'off' must not affect the fix behavior for unused variables: unused variable fixes must still use the default fix kind (DangerousSuggestion) when 'fix.variables' is not explicitly configured.

*   Setting 'fix.variables' to 'off' must not affect the fix behavior for unused imports: unused import fixes must still use the default fix kind (DangerousSuggestion) when 'fix.imports' is not explicitly configured.

*   When 'fix.imports' is set to 'fix', unused import violations must produce a DangerousFix (not a DangerousSuggestion).

*   When 'fix.variables' is set to 'fix', unused variable violations must produce a DangerousFix (not a DangerousSuggestion).

*   The default value for both 'fix.imports' and 'fix.variables' when the 'fix' object is absent or a sub-option is omitted must be 'suggestion', preserving the existing DangerousSuggestion behavior.

*   When the 'fix' object specifies only one sub-option (sparse configuration), the omitted sub-option must independently default to 'suggestion'; e.g., providing only 'fix.variables: off' must leave 'fix.imports' defaulting to 'suggestion'.

*   The NoUnusedVarsOptions struct must expose a 'fix' field of type NoUnusedVarsFixOptions, which holds an 'imports' field and a 'variables' field both of type NoUnusedVarsFixMode.

*   The NoUnusedVarsFixMode enum must have three variants: Off, Suggestion (default), and Fix, located in crates/oxc_linter/src/rules/eslint/no_unused_vars/options.rs.

*   Invalid values for 'fix.imports' or 'fix.variables' (e.g., an unrecognized string like 'bad-mode' or a non-string value like 42) must be rejected with a parse error when converting the options object.


*   Interface details: Type: Enum
Name: NoUnusedVarsFixMode
Location: crates/oxc_linter/src/rules/eslint/no_unused_vars/options.rs
Description: Controls the fix kind emitted for a category of unused declaration. Must be pub and derive Default (with Suggestion as default), Debug, Clone, Copy, PartialEq, Eq. Must be serialized with kebab-case (so "Off" → "off", "Suggestion" → "suggestion", "Fix" → "fix").
Variants:
  - Off       — disables any auto-fix or suggestion for this category
  - Suggestion — emits a suggestion-style fix (default variant)
  - Fix        — emits an automatic fix

Type: Struct
Name: NoUnusedVarsFixOptions
Location: crates/oxc_linter/src/rules/eslint/no_unused_vars/options.rs
Description: Holds per-category fix mode configuration. Must be pub, derive Default, Debug, Clone, and be serialized with camelCase field names.
Fields:
  - imports: NoUnusedVarsFixMode   — controls fix mode for unused imports
  - variables: NoUnusedVarsFixMode — controls fix mode for unused variables (including catch bindings)

Type: Field addition on existing struct
Name: fix
Location: crates/oxc_linter/src/rules/eslint/no_unused_vars/options.rs (on NoUnusedVarsOptions)
Description: A new pub field `fix: NoUnusedVarsFixOptions` added to the existing NoUnusedVarsOptions struct. Must default to NoUnusedVarsFixOptions::default(). Must be parsed from the rule's JSON configuration object under the key "fix", e.g. `{ "fix": { "imports": "off", "variables": "fix" } }`. Omitted sub-keys in the "fix" object must individually default to Suggestion.

Type: Function (private)
Name: report_with_fix_mode
Location: crates/oxc_linter/src/rules/eslint/no_unused_vars/mod.rs (impl NoUnusedVars)
Signature: fn report_with_fix_mode<'a, F>(mode: NoUnusedVarsFixMode, ctx: &LintContext<'a>, diagnostic: OxcDiagnostic, fix: F) where F: FnOnce(RuleFixer<'_, 'a>) -> RuleFix
Description: Dispatches the diagnostic based on mode: Off → plain diagnostic (no fix), Suggestion → diagnostic_with_fix_of_kind using FixKind::Suggestion, Fix → diagnostic_with_fix_of_kind using FixKind::Fix. Because the rule is declared as dangerous, FixKind::Suggestion becomes DangerousSuggestion and FixKind::Fix becomes DangerousFix at the framework level.

Note on FixKind values observed in tests:
- When fix mode is "suggestion" (or default), observed FixKind is DangerousSuggestion
- When fix mode is "fix", observed FixKind is DangerousFix
- When fix mode is "off", no fix is emitted at all (diagnostic only)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.