Implement improvements to the editor's autocomplete system by adding variable name completions inside template-style curly brace interpolation blocks and updating the SQL completion store to reflect changes in local dataset tables.

*   Create a new file at `frontend/src/core/codemirror/language/embedded-python.ts` to export the following functions:
    *   `parsePython(pythonParser: Parser, isActivated: () => boolean): MarkdownConfig`
        *   Return a `MarkdownConfig` with a `defineNodes` array containing two entries: the first with `name` "Python" and the second with `name` "PythonMark".
        *   Ensure the inline parser returns -1 when encountering a double open curly brace sequence or when `isActivated()` returns false.

    *   `embeddedPythonCompletions(isActivated: () => boolean): Extension`
        *   Return a CodeMirror Extension array where the first element is a Python language data facet extension.
        *   Ensure the autocomplete function returns null if `isActivated()` is false, `context.explicit` is false, or `context.matchBefore` returns null.
        *   Provide a `CompletionResult` with options from the global `variablesAtom` state when a word match is found.

    *   `variableCompletionSource(context: CompletionContext): CompletionResult | null`
        *   Return null if no open brace exists before the cursor, a closing brace appears after the last open brace, a double-brace escape sequence is detected, or `context.matchBefore` returns null.
        *   Return a `CompletionResult` with options from the global `variablesAtom` state when inside a valid single-brace block and a word match exists.

*   Update the `SQLCompletionStore` class in `frontend/src/core/codemirror/language/sql.ts`:
    *   Modify `getCompletionSource(connectionName: ConnectionName): SQLConfig | null` to be reactive to changes in local dataset tables.
        *   Ensure that after `datasetsAtom` is updated, the method returns a fresh `SQLConfig` reflecting the updated local tables.
        *   Merge remote connection tables with local dataset tables in the `schema` property.
        *   Update the `defaultSchema` property to reflect changes in the connection's `default_schema`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.