Update the `copy-template-dir` utility to accommodate the new API changes in the `readdirp` library. Ensure that the template directory copying functionality works correctly with the updated library version.

*   Update the `readdirp` package in the project dependencies:
    *   Change the version in `package.json` and `package-lock.json` to 4.x, upgrading from the previously pinned 3.x version.

*   Modify the `copy-template-dir` implementation:
    *   In `src/utils/copy-template-dir/copy-template-dir.ts`, change the import statement for `readdirp` from a default import to named imports as per the v4 API changes.
    *   Ensure that the `copyTemplateDir` function continues to correctly copy all template files from a source directory to a destination directory, preserving file paths and content.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.