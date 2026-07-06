Implement a configurable error verbosity setting for the CLI to allow users to choose between "low" and "full" verbosity modes, with "low" as the default. Ensure that in "low" verbosity mode, recoverable error indicators are suppressed, while "full" verbosity mode retains all current detailed behaviors. Debug mode should always behave as full verbosity.

Requirements:

*   Update the settings schema:
    *   Modify `getSettingsSchema()` in `packages/cli/src/config/settingsSchema.ts` to include an `errorVerbosity` property under the `ui` section.
    *   Set the type to 'enum', default value to 'low', and options to ['low', 'full'].

*   Modify UI components:
    *   In `DetailedMessagesDisplay`, hide the '(F12 to close)' hint when `errorVerbosity` is 'low'; show it when 'full'.
    *   In `Footer`, hide the error summary (error count and 'F12 for details' hint) when `errorVerbosity` is 'low', unless in debug mode.
    *   Ensure `Footer` shows the error summary in debug mode regardless of verbosity setting.

*   Adjust tool call displays:
    *   In `ToolGroupMessage`, hide errored tool calls (status: Error) when `errorVerbosity` is 'low', unless `isClientInitiated` is true.
    *   Update `mapToDisplay` in `packages/cli/src/ui/hooks/toolMapping.ts` to propagate `isClientInitiated` from each tool call's request to the display output.

*   Handle execution stops:
    *   In the stream handler, when a `STOP_EXECUTION` error occurs in low verbosity mode, add to history:
        1.  Note: 'Some internal tool attempts failed before this final error'.
        2.  Stop message: 'Agent execution stopped: <reason>'.
        3.  Failure hint: 'This request failed. Press F12 for diagnostics'.
    *   In full verbosity mode, do not add the suppressed-error note or failure hint.

*   Update hooks:
    *   Modify `useLoadingIndicator` in `packages/cli/src/ui/hooks/useLoadingIndicator.ts` to accept an `errorVerbosity` parameter.
        *   In low verbosity mode, do not use the generic phrase for the first retry attempt.
        *   Use "This is taking a bit longer, we're still on it." for retry attempts 2+.
    *   Modify `useQuotaAndFallback` in `packages/cli/src/ui/hooks/useQuotaAndFallback.ts` to accept an `errorVerbosity` parameter.
        *   In low verbosity mode, return 'retry_once' for retryable quota errors without prompting the user.
        *   Ensure terminal quota errors still prompt the user.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.