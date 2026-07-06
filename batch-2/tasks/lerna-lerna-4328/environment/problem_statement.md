## Description

The project currently depends on several small, deprecated third-party packages for terminal utility tasks: one for detecting whether the terminal supports Unicode, one for detecting the terminal's color depth, and one for generating ANSI/VT100 escape sequences to control cursor position and display. These packages are no longer actively maintained and have been deprecated by their authors, which means they represent ongoing maintenance risk without any path to fixes or updates.

Since these packages are very small and self-contained, we should inline their logic directly into the codebase rather than keeping them as external dependencies. This removes the deprecated packages from the dependency tree entirely while preserving the exact same runtime behavior.

## Expected Behavior

- A local module exists for detecting Unicode support, exported as a named function rather than a default export. The logic reads locale environment variables in priority order and matches the UTF-8 encoding variant case-insensitively.
- A local module exists for detecting terminal color support level. It inspects the stream's TTY status, the OS platform, and environment variables (including terminal program, CI markers, and TERM string) to determine whether the terminal supports no color, basic color, 256 colors, or 16 million colors. The result is a structured object with a numeric level plus individual boolean flags.
- A local module exists that generates standard ANSI/VT100 terminal control escape sequences: cursor movement in multiple directions (with optional count), erasing data or lines, hiding/showing the cursor, setting color/style, moving to absolute positions, moving to the start of line, and emitting a beep.

## Why This Matters

Removing deprecated external dependencies reduces security surface area, eliminates dependency audit warnings, and makes the codebase self-sufficient for these low-level terminal features. It also makes it possible to add TypeScript types to the previously untyped packages.
