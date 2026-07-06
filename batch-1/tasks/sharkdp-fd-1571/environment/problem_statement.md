## Description

Many modern terminal emulators support clickable hyperlinks, where text in the terminal can be rendered as a link that opens a file or URL when clicked. Currently, `fd` outputs plain file paths with no way to make them clickable, which means users have to manually copy-paste paths to open them.

It would be very useful to have an option to wrap output paths in terminal hyperlinks, pointing to the corresponding file. This would make search results directly navigable in terminals that support this feature.

## Expected Behavior

- A new option for controlling hyperlink output (accepting "always", "never", or "auto") should be added
- When set to "always", every output path should be wrapped in terminal hyperlink escape codes pointing to a file URL for that path
- The file URL should include the system hostname (on Unix systems) followed by the absolute path
- Default behavior should remain unchanged (no hyperlinks)
- The "auto" mode should enable hyperlinks whenever other terminal formatting features are active

## Why This Matters

Users working in interactive terminal sessions with compatible terminal emulators would be able to click directly on `fd` search results to open files, rather than having to copy paths manually. This significantly improves the interactive workflow when exploring large codebases or directory trees.
