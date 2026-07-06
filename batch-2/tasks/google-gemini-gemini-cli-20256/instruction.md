Unify the CLI commands for session management by integrating checkpoint subcommands into the session browser command. Ensure both commands provide a consistent user experience with autocomplete suggestions and error messages.

*   Update the `chatCommand` in `packages/cli/src/ui/commands/chatCommand.ts`:
    *   Set the description to 'Browse auto-saved conversations and manage chat checkpoints'.
    *   Ensure `autoExecute` is true and include exactly 6 subcommands: list, save, resume, delete, share, and a hidden 'checkpoints' subcommand.
    *   Use the prefix '/resume' in error messages for save, resume, and delete subcommands when the tag is missing.
*   Update the `resumeCommand` in `packages/cli/src/ui/commands/resumeCommand.ts`:
    *   Ensure it returns `{ type: 'dialog', dialog: 'sessionBrowser' }` when invoked with no arguments.
    *   Include visible subcommands: list, save, resume, delete, share.
    *   Add a hidden 'checkpoints' subcommand with the same subcommands.
*   Enhance the `Suggestion` interface in `packages/cli/src/ui/components/SuggestionsDisplay.tsx`:
    *   Add optional fields: `insertValue`, `sectionTitle`, and `submitValue`.
*   Modify the `SuggestionsDisplay` component:
    *   Render section headers as '-- <sectionTitle> --' in slash mode when `sectionTitle` changes.
*   Adjust the `handleAutocomplete` function in `useCommandCompletion`:
    *   Use `insertValue` instead of `value` for updating the text buffer if present.
*   Update the `useSlashCompletion` hook:
    *   Ensure the first suggestion for '/chat' or '/resume' is an auto-session entry with label 'list', sectionTitle 'auto', and submitValue set to the full command.
    *   Show the same grouped menu for unique partial prefixes like '/resum', with `isPerfectMatch` as false.
*   In `BuiltinCommandLoader`, add the `debug` subcommand to both commands and nested 'checkpoints' in nightly builds only.
*   Extend the `SlashCommand` interface in `packages/cli/src/ui/commands/types.ts`:
    *   Include an optional `suggestionGroup` field to set `sectionTitle` in suggestions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.