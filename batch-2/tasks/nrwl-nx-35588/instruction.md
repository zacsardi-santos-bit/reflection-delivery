We're removing two long-deprecated options from our Jest integration and need migration scripts to help existing workspaces upgrade automatically.

*   The migrate-jest-configuration-skip-setup-file migration must rewrite `skipSetupFile: true` to `setupFile: 'none'` in the `@nx/jest:configuration` generator defaults stored in nx.json (flat form: `generators['@nx/jest:configuration']`).

*   The migrate-jest-configuration-skip-setup-file migration must drop `skipSetupFile: false` from the `@nx/jest:configuration` generator defaults without adding any `setupFile` entry.

*   When both `skipSetupFile: true` and an existing `setupFile` value are present together in the same generator defaults object, the migration must preserve the existing `setupFile` value and only remove `skipSetupFile` (no clobbering).

*   The migrate-jest-configuration-skip-setup-file migration must also handle the nested generator defaults form, where options sit under `generators['@nx/jest']['configuration']` instead of the flat `generators['@nx/jest:configuration']` key.

*   After stripping `skipSetupFile`, if the resulting generator defaults object is empty, the migration must remove the entire key from `generators` (i.e., the `@nx/jest:configuration` key must be undefined afterward).

*   The migrate-jest-configuration-skip-setup-file migration must handle generator defaults stored on individual project configurations (project.json), not only in nx.json.

*   The migrate-jest-configuration-skip-setup-file migration must leave unrelated generator defaults (e.g., entries for other generators like `@nx/react:application`) completely untouched.

*   The migrate-jest-executor-setup-file migration must remove the `setupFile` property from the `@nx/jest:jest` executor's target options in project configuration and write the path into the `setupFilesAfterEnv` array in the referenced Jest config file, expressed as `<rootDir>`-relative (e.g., `'<rootDir>/src/test-setup.ts'`).

*   The migration must support both CommonJS (`module.exports = {}`) and ESM (`export default {}`) Jest config file formats when inserting `setupFilesAfterEnv`.

*   When a `setupFilesAfterEnv` array already exists in the Jest config, the migration must append to it rather than replace it, and must not create duplicate entries — including recognizing that `./src/test-setup.ts` and `<rootDir>/src/test-setup.ts` refer to the same file.

*   When the `setupFilesAfterEnv` property key is quoted (e.g., `'setupFilesAfterEnv': [...]`), the migration must still append to that single property and must not insert a second `setupFilesAfterEnv` property.

*   When the Jest config file contains a literal string `rootDir` value, the migration must resolve the setup file path relative to that rootDir and express it under `<rootDir>` accordingly (e.g., a `rootDir: '../../'` resolving to the workspace root means the entry becomes `'<rootDir>/apps/app1/src/test-setup.ts'`).

*   When the Jest config file contains a non-literal (dynamic) `rootDir` expression, the migration must leave the Jest config file untouched, still strip the `setupFile` option from the executor options, and return a follow-up callback function.

*   When the Jest config file is not in the same directory as the project root, the migration must compute the `<rootDir>`-relative path using the Jest config file's own directory as the base (since Jest defaults `rootDir` to the config file's directory).

*   The migration must resolve the `jestConfig` file path from `nx.json targetDefaults` (with `{projectRoot}` token support) when the target's own options do not specify `jestConfig`.

*   When the Jest config file contains only object-spread entries at the top level (e.g., `{ ...nxPreset, displayName: 'app1' }`), the migration must insert `setupFilesAfterEnv` using optional-chaining null-coalescing spread syntax: `setupFilesAfterEnv: [...(spread as any)?.setupFilesAfterEnv ?? [], '<rootDir>/src/test-setup.ts']`.

*   When multiple spreads are present in the Jest config object, the migration must use last-wins semantics: the last spread's `setupFilesAfterEnv` takes precedence, chained via `??` fallback to earlier spreads.

*   When an explicit `setupFilesAfterEnv` array is already in the Jest config and is followed by a spread (which would override it at runtime), the migration must leave the Jest config untouched and return a follow-up callback function.

*   When `setupFile` appears only inside a named configuration block (e.g., `configurations.ci`) and not in the base executor `options`, the migration must leave the Jest config file unchanged, strip the `setupFile` option from the configuration, and return a follow-up callback function.

*   The migration must remove `setupFile` from `nx.json targetDefaults` for the `@nx/jest:jest` executor.

*   When two targets in the same project share the same `jestConfig` file but specify different `setupFile` values, the migration must write only the first target's setup file into the shared jest config, strip `setupFile` from both targets' options, and return a follow-up callback function.

*   When both `setupFile` and `setupFilesAfterEnv` exist in the same executor options scope, the migration must strip `setupFile` from the executor options, preserve the `setupFilesAfterEnv` passthrough as-is, leave the Jest config file untouched, and return a follow-up callback function.

*   When a named configuration overrides `jestConfig` (a different config file from the base) and the base target has a `setupFile`, the migration must write the inherited setup file into both the base Jest config and the configuration's Jest config.

*   When a named configuration overrides `jestConfig` and specifies the same `setupFile` as the base, the migration must write the setup file to each Jest config exactly once (no duplicates — each config file should contain only one occurrence of the setup file path).

*   When a named configuration contains a `setupFilesAfterEnv` passthrough that could override the base `setupFilesAfterEnv` after migration, the migration must still migrate the base setup file normally but return a follow-up callback function to surface the regression risk.

*   When a named configuration overrides both `jestConfig` and `setupFile` with values different from the base, the migration must write each setup file into its corresponding jest config file without leaking values between them.

*   When the Jest config file does not export a static object literal (e.g., exports an async function), the migration must strip `setupFile` from the executor options in project.json but leave the Jest config file completely untouched, and return a follow-up callback function.

*   When a target has no `setupFile` in its options (and none is inherited), the migration must leave the project configuration and Jest config file completely untouched.

*   The migration must also process targets that inherit `setupFile` from `nx.json targetDefaults` (using `{projectRoot}` tokens): the resolved setup file must be written to the per-project Jest config, and the `setupFile` option must be removed from targetDefaults.


*   Interface details: Type: Function
Name: default (migrate-jest-configuration-skip-setup-file)
Location: packages/jest/src/migrations/update-23-0-0/migrate-jest-configuration-skip-setup-file.ts
Signature: default(tree: Tree): Promise<void>
Description: Migration that rewrites the deprecated `skipSetupFile` generator default option to its modern equivalent. Reads and rewrites generator defaults in nx.json and per-project configuration files.

Type: Function
Name: default (migrate-jest-executor-setup-file)
Location: packages/jest/src/migrations/update-23-0-0/migrate-jest-executor-setup-file.ts
Signature: default(tree: Tree): Promise<(() => void) | void>
Description: Migration that moves the deprecated `setupFile` executor option into the Jest config file's `setupFilesAfterEnv` array. Returns a follow-up callback function when it cannot fully automate the migration (e.g., dynamic rootDir, config-only setupFile, ambiguous spreads, non-static config). Returns void when migration completes fully automatically.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.