Implement a JSON output mode for the eval inventory tool to produce a structured, machine-readable report. Ensure the JSON includes a version marker, generation timestamp, summary, case details, and diagnostics, with all file paths as relative. Provide error handling for missing directories and allow timestamp overrides for reproducible builds.

*   Update `InventoryResult` type:
    *   Add `repoRoot: string` to represent the repository root directory.

*   Modify `collectInventory(repoRoot: string): Promise<InventoryResult>`:
    *   Set `result.repoRoot` to the input `repoRoot`.
    *   Throw an error with message matching `/evals directory not found/` if the `evals` directory is missing under `repoRoot`.

*   Update `formatInventoryReport(result: InventoryResult): string`:
    *   Use relative paths for diagnostic file locations.
    *   Include cases with unlisted policies, appending them after known policies.

*   Implement `formatInventoryJson(result: InventoryResult, now?: Date): string`:
    *   Export from `scripts/utils/eval-inventory.ts`.
    *   Return a JSON string with 2-space indentation.
    *   Include fields: `version` (set to 1), `generated` (ISO-8601 datetime), `summary`, `cases`, and `diagnostics`.
    *   Ensure all file paths are relative.
    *   Produce deterministic output for identical inputs.
    *   Override timestamp using environment variables:
        *   `SOURCE_DATE_EPOCH` as Unix timestamp in seconds.
        *   `EVAL_INVENTORY_STABLE_DATE` or `EVAL_INVENTORY_DETERMINISTIC` as Unix epoch 0.
        *   Default to current time if no overrides and `now` is not provided.

*   Export `InventoryJsonOutput` type from `scripts/utils/eval-inventory.ts`:
    *   Include: `version`, `generated`, `summary` (with `totalFiles`, `totalCases`, `totalDiagnostics`, `byPolicy`), `cases`, and `diagnostics`.

*   Ensure JSON output structure:
    *   `summary` contains `totalFiles`, `totalCases`, `totalDiagnostics`, `byPolicy`.
    *   `cases` array includes fields: `name`, `filePath`, `helperName`, `baseHelperName`, `policy`, `suiteName`, `suiteType`, `timeout`, `hasFiles`, `hasPrompt`, `location`.
    *   `diagnostics` array includes fields: `severity`, `message`, `filePath`, `location`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.