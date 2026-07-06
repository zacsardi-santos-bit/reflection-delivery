Implement a migration to update Playwright configuration files in an Nx workspace. Ensure that each configuration file includes browser project definitions for Chromium, Firefox, and WebKit, unless such definitions already exist.

*   Implement the migration function as the default export in `packages/playwright/src/migrations/update-17-3-1/add-project-to-config.ts`.
*   Define the function signature as `async function update(tree: Tree): Promise<void>`.
*   Ensure the function:
    *   Scans all projects in the workspace for a `playwright.config.ts` file located in each project's root directory.
    *   Checks if the `playwright.config.ts` file lacks a 'projects' property inside its `defineConfig` call.
        *   If missing, add a `projects` array with entries for:
            *   Chromium using 'Desktop Chrome'
            *   Firefox using 'Desktop Firefox'
            *   WebKit using 'Desktop Safari'
        *   Ensure the `devices` identifier is imported from '@playwright/test'.
            *   Update an existing import declaration or add a new one if necessary.
    *   Leaves any `playwright.config.ts` file unchanged if it already contains a 'projects' property in the `defineConfig` call.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.