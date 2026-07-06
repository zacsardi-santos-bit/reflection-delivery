Migrate the commitlint CLI package's test suite to the unified test framework used across the monorepo. Update the project-wide test runner configuration to include the CLI package's tests and remove any dependencies and configurations related to the old testing framework. Enhance the shared test utility package to support an optional directory parameter for fixture resolution.

*   Update the jest test runner configuration:
    *   Include the pattern '**/@commitlint/cli/src/*.test.js?(x)' in the testMatch array to ensure CLI package test files are executed.

*   Remove the ava testing framework from the CLI package:
    *   Delete the ava configuration block.
    *   Remove the 'test' script that invoked ava.
    *   Eliminate the ava dependency from the CLI package configuration.

*   Modify the CLI package's 'start' script:
    *   Ensure it references only the watch build step, excluding any reference to the ava test runner.

*   Update shared test utility package:
    *   Modify `fix.bootstrap` function in `@packages/test/src/fix.ts` to accept an optional `directory` parameter of string type.
        *   Pass this parameter to `pkgDir()` to resolve the package root relative to the specified directory.
    *   Modify `git.bootstrap` function in `@packages/test/src/git.ts` to accept an optional `directory` parameter.
        *   Pass this parameter through to `fix.bootstrap`.
    *   Modify `npm.bootstrap` function in `@packages/test/src/npm.ts` to accept an optional `directory` parameter.
        *   Pass this parameter through to `git.bootstrap`.

*   Ensure the @commitlint/test package's configuration:
    *   Set the 'main' field to 'lib/index.js'.
    *   Include a 'types' field pointing to 'lib/index.d.ts'.

*   Verify the CLI binary used by tests:
    *   Ensure it is located at `lib/cli.js` within the CLI package and is resolvable as '../lib/cli.js' relative to the test file in `src/`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.