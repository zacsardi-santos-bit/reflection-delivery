Extend the CLI tool to recognize and support additional editors: Sublime Text, Lapce, Nova, BBEdit, emacsclient, and micro. Implement editor-related features such as CLI-driven diff viewing, sandbox mode compatibility, and configuration validation.

*   Update the `EditorType` union type in `packages/core/src/utils/editor.ts` to include:
    *   'sublimetext', 'lapce', 'nova', 'bbedit', 'emacsclient', 'micro'.

*   Implement `isValidEditorType(editor: string): boolean` in `packages/core/src/utils/editor.ts`:
    *   Return true for recognized editor identifiers.
    *   Return false for command names, strings with spaces, empty strings, and unrecognized strings.

*   Implement `getEditorWaitFlag(editor: EditorType): string` in `packages/core/src/utils/editor.ts`:
    *   Return '-w' for 'sublimetext'.
    *   Return '--wait' for all other GUI editors.

*   Implement `resolveEditorTypeFromCommand(command: string): EditorType | undefined` in `packages/core/src/utils/editor.ts`:
    *   Perform case-insensitive resolution of command names to editor types.
    *   Return undefined for unrecognized or empty strings.

*   Implement `getEditorExtraArgs(editor: EditorType, options?: { newWindow?: boolean }): string[]` in `packages/core/src/utils/editor.ts`:
    *   Return ['-nw'] for 'emacsclient'.
    *   Return ['--new-window'] for VS Code-family editors if `options.newWindow` is true.
    *   Return [] for all other editors.

*   Update `getDiffCommand` in `packages/core/src/utils/editor.ts`:
    *   For 'emacsclient', return a command using Emacs Lisp string-escaped paths.
    *   For 'sublimetext', 'lapce', 'nova', and 'micro', return null.

*   Update `allowEditorTypeInSandbox` in `packages/core/src/utils/editor.ts`:
    *   Return true for 'emacsclient'.
    *   Return false for 'sublimetext', 'lapce', 'nova', and 'bbedit'.

*   Treat 'emacsclient' as a terminal editor in editor resolution logic:
    *   Use the synchronous spawn path.
    *   Use the asynchronous spawn path for new GUI editors.

*   Ensure `openEditorInNewWindow` setting is accessible via `useSettings()` in `packages/cli/src/ui/contexts/SettingsContext.ts`:
    *   The `useTextBuffer` hook should read this setting to determine new-window behavior for VS Code-family editors.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.