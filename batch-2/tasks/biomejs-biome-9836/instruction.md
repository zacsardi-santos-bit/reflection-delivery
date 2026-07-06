I'm working on improving Biome's ESLint migration tool.

*   The `merge_biome_config_with_eslint` function must be implemented as a public (crate-visible) function in `crates/biome_cli/src/execute/migrate/eslint_to_biome.rs`. It must accept an existing Biome configuration, an ESLint configuration (as `AnyConfigData`), and migration options, merge the ESLint config into the Biome config, and return the updated Biome configuration along with migration results.

*   A fixture-driven test runner must be added inside the `tests` module of `crates/biome_cli/src/execute/migrate/eslint_to_biome.rs`. It must use `tests_macros::gen_tests!` with the glob `"tests/specs/migrate_eslint/**/*.{json,jsonc}"` and the module grouping mode to auto-discover JSONC fixture files and generate test names from directory and file names. The generated test function is named `run_migrator_spec_test`.

*   The `run_migrator_spec_test` function must read the JSONC fixture file, extract the `eslint` and `biome` keys, call `merge_biome_config_with_eslint` with `include_inspired: true` and `include_nursery: true`, serialize the output as formatted JSON, and compare it to an insta snapshot stored adjacent to the fixture file.

*   The `tests_macros` crate must be added as a dev-dependency in `crates/biome_cli/Cargo.toml`.

*   When `jest/consistent-test-it` is set to severity `"error"` with no options, the migration must produce `nursery/useConsistentTestIt` at error level with options `{ "function": "test", "withinDescribe": "it" }`.

*   When `jest/consistent-test-it` is configured with options `{"fn": "it", "withinDescribe": "test"}`, the migration must produce `nursery/useConsistentTestIt` at error level with options `{ "function": "it", "withinDescribe": "test" }`. The ESLint option key `fn` must be mapped to the Biome option key `function`.

*   When `vitest/consistent-test-it` is configured with options `{"fn": "it", "withinDescribe": "test"}`, the migration must produce `nursery/useConsistentTestIt` at error level with options `{ "function": "it", "withinDescribe": "test" }`, identical to the jest variant mapping.

*   When `jsx-a11y/aria-role` is configured with `{"allowedInvalidRoles": ["text"], "ignoreNonDOM": true}`, the migration must produce `a11y/useValidAriaRole` at error level with options `{ "allowInvalidRoles": ["text"], "ignoreNonDom": true }`. The ESLint option `allowedInvalidRoles` maps to `allowInvalidRoles`; `ignoreNonDOM` maps to `ignoreNonDom`.

*   When `no-restricted-globals` is configured with a mix of plain string entries and object entries (`{"name": "...", "message": "..."}`), the migration must produce `style/noRestrictedGlobals` at error level with `{ "deniedGlobals": { ... } }`. Plain string entries must use `"TODO: Add a custom message here."` as the message value. Object entries with a `message` field must preserve the message exactly.

*   When `@typescript-eslint/array-type` is configured with `{"default": "generic"}`, the migration must produce `style/useConsistentArrayType` at error level with options `{ "syntax": "generic" }`.

*   When `@typescript-eslint/consistent-type-imports` is configured with `{"fixStyle": "inline-type-imports"}`, the migration must produce `style/useImportType` at error level with options `{ "style": "inlineType" }`. The ESLint value `"inline-type-imports"` maps to the Biome value `"inlineType"`.

*   When `@typescript-eslint/explicit-member-accessibility` is configured with `{"accessibility": "explicit", "overrides": {...}}`, the migration must produce `style/useConsistentMemberAccessibility` at error level with options `{ "accessibility": "explicit" }`. The `overrides` option must not be migrated.

*   When `@typescript-eslint/naming-convention` is configured with a list of selector-based conventions, the migration must produce `style/useNamingConvention` at error level. Global options must include `"strictCase": false` and `"requireAscii": false`. The `conventions` array must be constructed in this order and with these mappings: (1) `selector: "enumMember", format: ["UPPER_CASE"]` → `{ "selector": { "kind": "enumMember" }, "formats": ["CONSTANT_CASE"] }` (ESLint UPPER_CASE becomes Biome CONSTANT_CASE); (2) `selector: "interface", prefix: ["I", "IO"]` → `{ "selector": { "kind": "interface" }, "match": "(?:I|IO)(.*)" }`; (3) `selector: "property", modifiers: ["private"], format: ["strictCamelCase"], leadingUnderscore: "require"` → `{ "selector": { "kind": "classProperty", "modifiers": ["private"] }, "match": "_([^_]*)", "formats": ["camelCase"] }` (strictCamelCase becomes camelCase, leadingUnderscore require becomes match prefix `_`); (4) `selector: "property", leadingUnderscore: "forbid"` expands to three entries for `classProperty`, `typeProperty`, and `objectLiteralProperty`, each with `"match": "([^_]*)"`. Entries with unsupported features (e.g. `types`) must be omitted.

*   When `unicorn/filename-case` is configured with `{"cases": {"camelCase": true, "pascalCase": true}}`, the migration must produce `style/useFilenamingConvention` at error level with options `{ "filenameCases": ["camelCase", "PascalCase"] }`. The ESLint key `pascalCase` maps to the Biome value `"PascalCase"` (capital P); `camelCase` maps to `"camelCase"`.

*   All rule migrations must produce a Biome config with `"recommended": false` set at the `linter.rules` level.


*   Interface details: Type: Function
Name: merge_biome_config_with_eslint
Location: crates/biome_cli/src/execute/migrate/eslint_to_biome.rs
Signature: merge_biome_config_with_eslint(biome_config: biome_config::Configuration, eslint_config: eslint_eslint::AnyConfigData, options: &MigrationOptions) -> (biome_config::Configuration, MigrationResults)
Description: Merges the result of converting an ESLint configuration into an existing Biome configuration. Calls the ESLint config's `into_biome_config` method with the provided options, merges the resulting Biome config into the provided base config, and returns the merged config together with migration results. Must be pub(crate) visible. Called by both the migration command handler and the test infrastructure.

Type: Function
Name: run_migrator_spec_test
Location: crates/biome_cli/src/execute/migrate/eslint_to_biome.rs (inside `mod tests`)
Signature: run_migrator_spec_test(input: &'static str, _: &str, _: &str, _: &str)
Description: Fixture-driven test runner for ESLint migration snapshot tests. Reads the JSONC fixture file at `input`, extracts the `"eslint"` and `"biome"` top-level keys as raw JSON, deserializes both into their respective config types, calls `merge_biome_config_with_eslint` with `MigrationOptions { include_inspired: true, include_nursery: true }`, serializes and formats the result as JSON, and then records an insta snapshot with the snapshot stored adjacent to the fixture file. The snapshot format is a Markdown document with three fenced JSON blocks: "ESLint Config", "Biome Config Before Migration", and "Biome Config After Migration". The `insta::with_settings!` call must set `prepend_module_to_snapshot => false` and `snapshot_path` to the fixture file's parent directory.

Type: Macro invocation (test discovery)
Name: gen_tests! (from tests_macros crate)
Location: crates/biome_cli/src/execute/migrate/eslint_to_biome.rs (inside `mod tests`, at module level)
Signature: tests_macros::gen_tests!{"tests/specs/migrate_eslint/**/*.{json,jsonc}", crate::execute::migrate::eslint_to_biome::tests::run_migrator_spec_test, "module"}
Description: Auto-discovers all `.json` and `.jsonc` fixture files under `crates/biome_cli/tests/specs/migrate_eslint/` and generates individual test functions. The `"module"` mode groups tests into nested modules named after directory components. This generates test names of the form `migrate_eslint::<directory_snake_case>::<file_snake_case>`, which must match the FAIL_TO_PASS test names. The `tests_macros` crate must be added as a dev-dependency in `crates/biome_cli/Cargo.toml`.

Type: Struct
Name: MigratorSpec
Location: crates/biome_cli/src/execute/migrate/eslint_to_biome.rs (inside `mod tests::test_utils`)
Signature: struct MigratorSpec { eslint_config: LegacyConfigData, biome_config: biome_config::Configuration }
Description: Helper struct used to deserialize and validate a fixture file. Has two fields: `eslint_config` (deserialized from the `"eslint"` key, renamed via `#[deserializable(rename = "eslint")]`) and `biome_config` (deserialized from the `"biome"` key, renamed via `#[deserializable(rename = "biome")]`). Must implement `Deserializable` via `biome_deserialize_macros::Deserializable`. Used in `run_migrator_spec_test` to validate that the fixture is well-formed before building the snapshot.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.