Implement a linter feature to resolve conflicts between two linting rules: one that flags imports outside the module top level and another that bans certain modules from being imported at the top level. Ensure that the "import should be at the top level" warning is suppressed when all imported modules or names are banned from top-level use.

*   Suppress the PLC0415 diagnostic for non-top-level imports when all modules or names are banned from top-level use.
    *   Do not emit PLC0415 for simple non-top-level imports if the module is in the banned list, regardless of alias usage.
    *   Suppress PLC0415 for 'from X import Y' or 'from X import Y as Z' if the fully-qualified name or parent module is banned.
    *   Suppress PLC0415 for 'from pkg import a, b, c' if the parent package is banned.
    *   Emit PLC0415 if a non-top-level import mixes banned and non-banned names.
*   Apply suppression consistently across function bodies, class bodies, and method bodies.
*   Update the `import_outside_top_level_with_banned` test function in `crates/ruff_linter/src/rules/pylint/mod.rs`.
    *   Enable `PreviewMode::Enabled`.
    *   Configure banned imports as `["foo_banned", "pkg_banned", "pkg.bar_banned"]`.
    *   Enable `Rule::BannedModuleLevelImports` and `Rule::ImportOutsideTopLevel`.
    *   Run `test_path` on `Path::new("pylint/import_outside_top_level_with_banned.py")`.
    *   Use `assert_messages!(diagnostics)` for snapshot assertion.
*   Create the snapshot file `crates/ruff_linter/src/rules/pylint/snapshots/ruff_linter__rules__pylint__tests__import_outside_top_level_with_banned.snap`.
    *   Include exactly 10 PLC0415 diagnostics at specified lines in the fixture file.
*   Modify the `banned_module_level_imports` function signature to accept `(checker: &mut Checker, stmt: &Stmt)`.
    *   Handle statement-level enumeration of import aliases internally.
*   Introduce a `BannedModuleImportPolicies` enum in the banned module-level imports rule file.
    *   Implement `IntoIterator` yielding `(NameMatchPolicy<'a>, AnyNodeRef<'a>)` pairs.
    *   Provide a constructor `BannedModuleImportPolicies::new(stmt: &'a Stmt, checker: &Checker) -> Self`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.