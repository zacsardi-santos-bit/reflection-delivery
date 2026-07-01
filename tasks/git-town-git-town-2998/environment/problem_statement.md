## Description

The Git Town configuration setup wizard is missing a step for managing command aliases. Aliases let users run Git Town commands with shorter git subcommands — for example, typing "git sync" instead of "git town sync". Currently there is no way to configure aliases through the setup wizard, and none of the supporting data structures or logic exist to power such a dialog.

## Expected Behavior

- The configuration wizard should include an alias selection dialog as its first step.
- The alias selection dialog should allow users to:
  - See all Git Town commands that can be aliased.
  - Toggle individual commands between: no alias, a Git Town alias, or keeping an existing external alias.
  - Select all commands at once or clear all selections at once.
- When a command has an existing alias pointing to Git Town, it should default to "selected".
- When a command has an existing alias pointing to some other command, it should default to "other".
- When a command has no existing alias, it should default to "none".
- After configuration, a readable summary should be shown: either "(all)", "(none)", or a comma-separated list of aliased command names.

## Supporting Utilities

The configuration domain package needs new utilities:
- A way to construct a typed command list from raw strings.
- A way to convert a typed command list back to strings.
- A defined type for the alias map itself.
- A renamed accessor that returns all aliasable commands as the typed slice.

## Why This Matters

Without this step, users completing the setup wizard have no guided way to enable or update Git Town aliases. The wizard should be a one-stop shop for complete Git Town configuration.
