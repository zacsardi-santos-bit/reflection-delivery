Consolidate plan mode settings into the general settings section of the configuration file. Update the feature description for clarity and ensure proper behavior when plan mode is disabled. Implement the necessary changes to the settings schema and CLI configuration handling.

*   Update the settings schema:
    *   Implement the `getSettingsSchema` function in `packages/cli/src/config/settingsSchema.ts` to include a `general.properties.plan` object.
    *   Ensure `general.properties.plan` contains a `properties.enabled` entry with:
        *   `type: 'boolean'`
        *   `category: 'General'`
        *   `default: true`
        *   `requiresRestart: true`
        *   `showInDialog: true`
        *   `description: 'Enable Plan Mode for read-only safety during planning.'`
    *   Ensure the `general.plan` object supports `enabled` (boolean) and `directory` (string) as sibling properties.

*   Modify CLI configuration handling:
    *   Implement the `loadCliConfig` function in `packages/cli/src/config/config.ts`.
    *   Read plan mode enabled status from `settings.general.plan.enabled` instead of `settings.experimental.plan`.
    *   When plan mode is disabled and `defaultApprovalMode` is 'plan' in settings (without a CLI flag), silently fall back to the default approval mode.
    *   When `--approval-mode=plan` is passed via CLI but plan mode is disabled, throw an error.

*   Update the `Settings` interface:
    *   Ensure the `general` section includes a `plan` property with optional `enabled?: boolean` and `directory?: string`.
    *   Remove plan mode enablement from the `experimental` section.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.