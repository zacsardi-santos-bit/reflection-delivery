Implement a migration rule to convert old-style 'include' and 'ignore' fields in Biome configuration files to a unified 'includes' field. Ensure the rule handles all configuration sections, supports JSONC syntax, and reports errors for unsupported glob patterns.

*   Implement a new migration rule named `Includes`:
    *   Declare `Includes` using `declare_migration!` and register it in the "migrations" group for version "2.0.0".
    *   Target configurations at version 2.0.0 or later.
    *   Emit a diagnostic with the message: 'include and ignore configurations have been replaced by the includes configuration.'
    *   Provide a fix that merges 'include' and 'ignore' fields into a single 'includes' field.
    *   Prepend '**' if only 'ignore' is present.
    *   Skip unsupported glob patterns and emit an informational diagnostic: 'This glob cannot be converted to the new glob format because it generates the follosing error: {error detail}'.

*   Ensure the rule applies to:
    *   'files', 'formatter', 'assists', and 'linter' top-level sections.
    *   Items within the 'overrides' array.

*   Handle JSONC syntax:
    *   Remove comments between 'include' and 'ignore' fields during migration.
    *   Maintain syntactic validity by handling trailing commas.

*   Implement the `to_biome_glob` function:
    *   Signature: `fn to_biome_glob(glob: &str, is_exception: bool) -> String`.
    *   Transform globs according to specified rules.
    *   Validate transformations with a `#[test]` function named `test_to_biome_glob`.

*   Update the spec test framework:
    *   Discover tests using the pattern `tests/specs/**/*.{json,jsonc}`.
    *   Use `JsonParserOptions::default().with_allow_comments().with_allow_trailing_commas()` for parsing.
    *   Require a `.version.txt` file for each test spec, with a panic if missing.

*   Ensure all existing test cases in 'all' and 'nurseryRules' have '.version.txt' files with content '1.5.0'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.