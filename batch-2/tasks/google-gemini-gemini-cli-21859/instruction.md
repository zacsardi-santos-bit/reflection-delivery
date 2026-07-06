Enable Plan Mode by default, removing the need for users to opt in via settings. Ensure that when the plan approval mode is selected through the command-line, it activates immediately without experimental gating. Update the settings and approval modes list to reflect this change.

*   Update the `Config.isPlanEnabled()` method:
    *   Ensure it returns `true` by default when the plan setting is not explicitly provided.
    *   Ensure it returns `false` only when plan is explicitly set to `false`.

*   Modify the `loadCliConfig()` function:
    *   Ensure that when the `--approval-mode=plan` CLI argument is used, the resulting config's `getApprovalMode()` method returns `ApprovalMode.PLAN`.
    *   Remove any requirement for an experimental opt-in setting for this behavior.

*   Update the `experimental.plan` entry in the settings schema:
    *   Set the default to `true`.
    *   Set the description to 'Enable Plan Mode.'.
    *   Ensure the type is 'boolean', category is 'Experimental', requiresRestart is `true`, and showInDialog is `true`.

*   Include Plan Mode in the list of available approval modes:
    *   Add an entry with id `ApprovalMode.PLAN`.
    *   Set the name to 'Plan' and description to 'Read-only mode'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.