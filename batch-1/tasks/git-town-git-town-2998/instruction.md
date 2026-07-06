Implement the alias configuration dialog for the Git Town setup wizard, allowing users to manage command aliases interactively. Develop the necessary data structures and logic to classify existing aliases and determine the result of user selections.

*   Update the configuration wizard:
    *   Include an alias selection dialog as the first step.
    *   Allow users to toggle command aliases between no alias, a Git Town alias, or keeping an existing external alias.
    *   Provide options to select all or clear all command aliases at once.
    *   Default command alias states based on existing configurations.

*   Modify the configuration domain package:
    *   Implement `AllAliasableCommands()` to return a typed `AliasableCommands` slice.
    *   Define `AliasableCommands` as a typed slice of `AliasableCommand` values with a `Strings()` method.
    *   Implement `NewAliasableCommand(command string)` to return the corresponding `AliasableCommand` constant.
    *   Implement `NewAliasableCommands(commands ...string)` to return an `AliasableCommands` slice.
    *   Define `Aliases` as `map[AliasableCommand]string`.

*   Implement alias selection logic:
    *   Define `AliasSelection` as an integer type with constants `AliasSelectionNone`, `AliasSelectionGT`, and `AliasSelectionOther`.
    *   Implement `NewAliasSelections(aliasableCommands, existingAliases)` to classify commands based on existing aliases.
    *   Implement `DetermineAliasResult(selections, allAliasableCommands, existingAliases)` to convert selections into a final `Aliases` map.
    *   Implement `DetermineAliasSelectionText(selectedCommands)` to produce a human-readable summary of selected commands.

*   Develop the `AliasesModel` struct in the CLI dialog:
    *   Include fields: `AllAliasableCommands`, `CurrentSelections`, `OriginalAliases`, and an embedded `BubbleList`.
    *   Implement `Checked()` to return commands with `AliasSelectionGT`.
    *   Implement `RotateCurrentEntry()` to update `CurrentSelections[Cursor]` using a state machine.
    *   Implement `SelectAll()` to set all `CurrentSelections` to `AliasSelectionGT`.
    *   Implement `SelectNone()` to set all `CurrentSelections` to `AliasSelectionNone`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.