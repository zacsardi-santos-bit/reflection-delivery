Implement consistent handling of file paths in plan mode by treating them as relative to the plans directory, not the workspace root. Update the AI-facing prompt to display relative paths, and ensure path security validation works with these changes.

*   Update the Config interface:
    *   Implement the `getProjectRoot()` method to return the project root directory as a string.
*   Modify the `validatePlanPath` function:
    *   Update the signature to `validatePlanPath(planPath: string, plansDir: string, projectRoot: string) -> Promise<string | null>`.
    *   Treat `planPath` as a relative path within the plans directory.
    *   Resolve the full path by joining `plansDir` and `planPath`.
    *   Ensure error messages use the original `planPath` argument, with the format: 'Access denied: plan path (${planPath}) must be within the designated plans directory (${plansDir}).'
*   Implement the `makeRelative` function:
    *   Export from `packages/core/src/utils/paths.ts`.
    *   Signature: `makeRelative(absolutePath: string, baseDir: string) -> string`.
    *   Return a relative path from `baseDir` to `absolutePath`.
*   Update the AI-facing prompt in plan mode:
    *   Include the relative path from the project root to the plans directory.
    *   Use the text: 'write .md plan files to `${relativePlansPath}/`', with `relativePlansPath` computed using `makeRelative(plansDir, projectRoot)`.
    *   Replace backslashes with forward slashes in the path.
*   Ensure the write file tool in plan mode:
    *   Resolves nested relative paths by joining them with the plans directory path.
    *   Creates any required intermediate directories.
*   Ensure the edit tool in plan mode:
    *   Accepts a simple relative filename.
    *   Resolves it by joining with the plans directory path.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.