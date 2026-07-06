Update the plan mode in the CLI to handle file paths more flexibly and predictably. Implement changes to ensure plan file references are relative to the plans directory, and adjust the system prompt to display paths relative to the project root. Enhance path validation and file handling to support nested directories and improve portability.

*   Implement the `getProjectRoot()` method in the Config interface:
    *   Return the absolute path to the project root directory.
    *   Add this method to `packages/core/src/config/config.ts`.

*   Modify the `validatePlanPath` function:
    *   Accept three parameters: `planPath`, `plansDir`, and `projectRoot`.
    *   Return `null` for valid paths or an error string for invalid paths.
    *   Return 'Plan file does not exist' if `planPath` does not exist within `plansDir`.
    *   Return 'Access denied: plan path (${planPath}) must be within the designated plans directory (${plansDir}).' if a symbolic link points outside `plansDir`.
    *   Implement this function in `packages/core/src/utils/planUtils.ts`.

*   Export and use the `makeRelative` function:
    *   Accept `absolutePath` and `basePath` parameters.
    *   Return the relative path from `basePath` to `absolutePath`.
    *   Implement this function in `packages/core/src/utils/paths.ts`.

*   Update the system prompt in plan mode:
    *   Display the plans directory as a relative path from the project root.
    *   Use `makeRelative(plansDir, projectRoot)` to compute the relative path.
    *   Format the write constraint message as: write .md plan files to `${relativePath}/`.

*   Adjust file handling in plan mode:
    *   Resolve write destinations using `path.join(plansDir, filePath)` for relative paths.
    *   Create intermediate directories as needed when writing files.
    *   Resolve edit tool targets using `path.join(plansDir, filePath)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.