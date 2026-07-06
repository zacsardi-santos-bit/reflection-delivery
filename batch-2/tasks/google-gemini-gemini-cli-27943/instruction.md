Implement defensive path handling in file tools to correctly process paths with an at-sign prefix and ensure security boundaries. Strip the at-sign prefix from paths when appropriate and handle path resolution errors gracefully.

*   Implement path stripping logic:
    *   For paths starting with '@/' or '@\', always strip the prefix.
    *   For paths starting with '@' without a separator, strip the prefix only if the first directory segment exists in the workspace.
*   Ensure security and error handling:
    *   Detect and return an error message containing 'Path not in workspace' if a resolved path is outside workspace boundaries.
    *   Detect circular symbolic link chains and return an error message containing 'Failed to resolve path'.
*   Update `getCorrectedFileContent` in `packages/core/src/tools/write-file.ts`:
    *   Validate paths against workspace boundaries.
    *   Handle circular symbolic link detection.
    *   In plan mode, resolve paths relative to `config.storage.getPlansDir()` and return original content on success.
*   Update `correctPath` in `packages/core/src/utils/pathCorrector.ts`:
    *   Apply defensive at-sign prefix stripping.
    *   Return a success object with the absolute path if the stripped path's first segment exists in the workspace.
*   Update `ReadFileTool` in `packages/core/src/tools/read-file.ts`:
    *   Apply at-sign prefix stripping logic.
    *   Ensure successful path resolution results in undefined error and correct file content retrieval.
*   Update `WriteFileTool` in `packages/core/src/tools/write-file.ts`:
    *   Apply the same at-sign prefix stripping logic.
    *   Prevent creation of literal "@"-prefixed directories when the stripped path's first segment exists.
*   Update `EditTool` in `packages/core/src/tools/edit.ts`:
    *   Apply at-sign prefix stripping logic.
    *   Ensure `getModifyContext` methods handle workspace boundary and symbolic link errors:
        *   `getCurrentContent` and `getProposedContent` must reject with 'Path not in workspace' for paths outside the workspace.
        *   Reject with 'Failed to resolve path' for circular symbolic link chains.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.