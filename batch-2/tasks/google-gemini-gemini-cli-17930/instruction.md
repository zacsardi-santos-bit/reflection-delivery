Implement a dialog-style component for displaying hooks in the CLI tool, replacing the current method of pushing messages into the UI feed. Ensure the dialog is interactive and separate from the message stream.

*   Modify `hooksCommand.action` in `packages/cli/src/ui/commands/hooksCommand.ts` to:
    *   Return an object with a 'type' property set to 'custom_dialog'.
    *   Include a 'component' property containing the HooksDialog element.

*   Create and export a `HooksDialog` component in `packages/cli/src/ui/components/HooksDialog.tsx`:
    *   Accept props: `hooks` (array of HookEntry objects), `onClose` (callback function), and `maxVisibleHooks` (optional number).
    *   Render hooks grouped by `eventName`.
    *   Display each hook's enabled/disabled status.
    *   Use `config.command` as the display name if `config.name` is absent.
    *   Include a security warning, source path, and a tips section.
    *   Display optional metadata: matcher, sequential, and timeout.

*   Define and export a `HookEntry` type in the same file:
    *   Include a `config` object with optional fields: `name`, `command`, `type`, `description`, `timeout`.
    *   Include required fields: `source` (string), `eventName` (string), `enabled` (boolean).
    *   Include optional fields: `matcher` (string), `sequential` (boolean).

*   Ensure `HooksDialog` functionality:
    *   Invoke `onClose` callback when Escape key is pressed.
    *   Support scrolling with `maxVisibleHooks`.
        *   Show scroll indicators (▲/▼) when hooks exceed `maxVisibleHooks`.
        *   Hide indicators when at the list's top or bottom.
    *   Allow scrolling with up/down arrow keys, clamped to list boundaries.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.