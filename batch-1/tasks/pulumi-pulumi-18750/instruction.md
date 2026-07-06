Update the Node.js SDK automation test suite to resolve TypeScript compilation issues and ensure tests run successfully. Switch the TypeScript transpilation approach to a more modern method and update stack naming conventions in specific tests.

*   Modify the mocha test runner configuration:
    *   Update `sdk/nodejs/package.json` to use `tsx` as the TypeScript transpiler with a Node.js import hook: set `node-option: ["import=tsx"]`.
    *   Remove the use of `ts-node/register` from the configuration.

*   Add `tsx` as a development dependency:
    *   Include `tsx` in the `devDependencies` section of `sdk/nodejs/package.json`.

*   Adjust TypeScript configuration:
    *   In `sdk/nodejs/tsconfig.json`, ensure the `ts-node` configuration section has `'files': true` to include all files specified in `tsconfig.json` when used.

*   Update stack naming in tests:
    *   For the test verifying concurrent configuration updates, use fully qualified stack names.
    *   Format stack names as `'organization/concurrent-config/int_test_<name>_<suffix>'` using `fullyQualifiedStackName("organization", "concurrent-config", ...)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.