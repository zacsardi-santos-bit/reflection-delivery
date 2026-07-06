Implement a simplified plan mode feature in the CLI tool by allowing the use of plain filenames instead of full directory-prefixed paths. Ensure that file edits during plan mode are redirected to the plans directory.

*   Update the `ExitPlanModeParams` interface:
    *   Rename the parameter to `plan_filename` to indicate that only a filename is expected.
*   Validate parameters:
    *   Reject empty or whitespace-only `plan_filename` with the message: `plan_filename is required.`
    *   Ensure `plan_filename` is a plain filename and resolve the full path internally using `path.basename(plan_filename)`.
    *   If `plan_filename` resolves to a symlink outside the plans directory, return: `Access denied: plan path (${resolvedPath}) must be within the designated plans directory (${plansDir}).`
*   Modify `validatePlanPath` function:
    *   Accept only two parameters: `(planPath: string, plansDir: string)`.
    *   Resolve the full path using `path.join(plansDir, path.basename(planPath))`.
*   Update `getExitPlanModeDefinition` function:
    *   Remove the `plansDir: string` parameter.
    *   Declare `plan_filename` as the required property with the description: `The filename of the finalized plan (e.g., "feature-x.md"). Do not provide an absolute path.`
*   Enhance the `Config` class:
    *   Add an `isPlanMode(): boolean` method to check if the system is in plan mode.
*   Redirect file writes during plan mode:
    *   When `config.isPlanMode()` returns true, write to `path.join(config.storage.getPlansDir(), path.basename(params.file_path))`.
    *   Ensure the result's `llmContent` matches `/Successfully modified file/`.
*   Update the snapshot file:
    *   Regenerate `packages/core/src/tools/definitions/__snapshots__/coreToolsModelSnapshots.test.ts.snap` using `vitest --update-snapshots` to reflect changes in the `exit_plan_mode` tool.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.