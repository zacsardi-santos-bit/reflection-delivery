Implement a function to validate project `package.json` files against organizational standards. Ensure that each package meets specific criteria or report violations with details.

*   Implement the `validateProjectPackageJson` function in `tools/workspace-plugin/src/conformance-rules/project-package-json/index.ts`.
    *   Export it as a named function.
    *   Accept four parameters:
        *   `projectPackageJson`: a `Record<string, unknown>` representing the parsed `package.json`.
        *   `sourceProject`: a `string` representing the project name.
        *   `sourceProjectRoot`: a `string` representing the project root path.
        *   `projectPackageJsonPath`: a `string` representing the absolute path to the `package.json` file.

*   Return an empty array if:
    *   `packageJson.private === true`.
    *   The package has a valid string name (either unscoped or scoped to `@nx/`), `publishConfig.access` set to `'public'`, and a non-empty `exports` object.

*   Return violations as an array of `ProjectFilesViolation` objects if:
    *   `packageJson.name` is not a string.
        *   Message: 'The project package.json should have a "name" field'.
    *   `packageJson.name` is scoped but not to `@nx/`.
        *   Message: 'The package name should be scoped to the @nx org'.
    *   `publishConfig.access` is not `'public'` for non-private packages.
        *   Message: 'Public packages should have "publishConfig": { "access": "public" } set in their package.json'.
    *   An `executors.json` file exists but `packageJson.executors` is not `'./executors.json'`.
        *   Message: 'The project has an executors.json, but does not reference "./executors.json" in the "executors" field of its package.json'.
    *   A `generators.json` file exists but `packageJson.generators` is not `'./generators.json'`.
        *   Message: 'The project has an generators.json, but does not reference "./generators.json" in the "generators" field of its package.json'.
    *   `packageJson.exports` is not an object or is an empty object.
        *   Message: 'The project package.json should have an "exports" object specified'.

*   Ensure each violation object has the shape:
    *   `{ file: string, message: string, sourceProject: string }`
    *   `file` is `projectPackageJsonPath`.
    *   `sourceProject` is `sourceProject`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.