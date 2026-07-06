Implement a command loading and conflict resolution system in the CLI that uses a namespace to identify the source of each command. Ensure that command names are resolved using a colon-separated format and that extension command descriptions are not automatically prefixed.

*   Update the `SlashCommand` interface in `packages/cli/src/ui/commands/types.ts`:
    *   Add an optional `namespace` field of type `string`.

*   Modify the `FileCommandLoader` class in `packages/cli/src/services/FileCommandLoader.ts`:
    *   Implement the `loadCommands(signal: AbortSignal): Promise<SlashCommand[]>` method to:
        *   Set the `namespace` field to `'user'` for commands loaded from the user commands directory.
        *   Set the `namespace` field to `'workspace'` for commands loaded from a project's commands directory.
        *   Set the `namespace` field to the extension's name for commands loaded from an extension.
        *   Ensure extension command descriptions are exactly as defined in the command's TOML file, without any bracket-prefixed extension name.

*   Update the `CommandService` class in `packages/cli/src/services/CommandService.ts`:
    *   Implement name resolution for commands with a `namespace` field using the format `namespace:originalName`.
    *   Handle conflicts by appending a numeric suffix, resulting in names like `namespace:name1`, `namespace:name2`, etc.
    *   Modify the `getConflicts()` method to:
        *   Report conflicts using the fully namespaced command name in the `name` field.
        *   Include a `renamedTo` property for each conflict loser, using the colon-separated namespaced format with a numeric suffix.
        *   Include the original command object with its `namespace` field in the `command` property for each conflict loser.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.