## Description

The CLI tool only recognizes a small set of editors, leaving users of several popular editors — including Sublime Text, Lapce, Nova, BBEdit, emacsclient, and micro — unable to configure their preferred editor. These editors should be added as first-class options with proper support for all editor-related features.

## Expected Behavior

- Sublime Text, Lapce, Nova, BBEdit, emacsclient, and micro should be recognized as valid editor choices.
- Editors that do not support CLI-driven diff viewing should gracefully indicate this (return nothing) rather than attempting to launch an unsupported command.
- The emacsclient editor should support CLI-driven diff viewing using its own built-in diff mechanism, with correct escaping of file paths.
- Sandbox mode compatibility should be correctly determined per editor: emacsclient (a terminal editor) should be allowed; the new GUI editors should not.
- There should be a utility to validate whether a string is a recognized editor identifier — useful for user-facing configuration validation.
- There should be a utility that looks up the canonical editor name from its executable command name, working case-insensitively.
- For editors that support it, a way to retrieve the correct wait flag and any extra startup arguments should be available. Sublime Text uses a different wait flag than other GUI editors. When the user has configured files to open in a new window, applicable editors should respect that setting.
- The text input hook should read the new-window preference from the settings context when deciding how to invoke an editor.

## Why This Matters

Users who rely on editors outside the originally supported set have no way to use their preferred tools with this CLI. Adding these editors ensures broader compatibility, and the new utility functions enable better configuration validation and editor resolution throughout the codebase.
