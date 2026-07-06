Implement the `FindWorkspaceRoot` function to detect if a Pulumi Node.js project is part of an npm or yarn workspace and determine the correct root directory for dependency installation. Ensure that the function correctly identifies workspace roots and handles edge cases where a project is not a workspace member.

*   Implement the `FindWorkspaceRoot` function in `sdk/nodejs/npm/workspaces.go`:
    *   Accept a `programDirectory` string parameter.
    *   Return a string representing the workspace root path and an error.
    *   Traverse parent directories from the given path to find a workspace root.
    *   Stop at the filesystem root without returning an error other than `ErrNotInWorkspace`.

*   Handle workspace detection:
    *   If the directory is part of an npm or yarn workspace, return the workspace root directory and a nil error.
    *   If the directory is not part of any workspace, return an empty string and the `ErrNotInWorkspace` error.

*   Export the `ErrNotInWorkspace` sentinel error in `sdk/nodejs/npm/workspaces.go`:
    *   Define it with the value `errors.New("not in a workspace")`.
    *   Ensure it is detectable via `errors.Is`.

*   Create necessary test data:
    *   Add `sdk/nodejs/npm/testdata/workspace/package.json` with a "workspaces" field listing "project/" as a member.
    *   Add `sdk/nodejs/npm/testdata/workspace/project/package.json` with valid content for the sub-project.
    *   Create a directory `sdk/nodejs/npm/testdata/nested/project/` representing a non-member project.
        *   Ensure `sdk/nodejs/npm/testdata/nested/package.json` exists but does not include "project" in any "workspaces" field.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.