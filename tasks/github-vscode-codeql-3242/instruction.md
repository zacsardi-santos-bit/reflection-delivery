Implement keyboard shortcut support for the suggestion dropdown in the VS Code extension's webview UI. Create a utility hook to maintain stable callback references across re-renders. Extract and share a mock object utility for testing purposes.

*   Implement the `useEffectEvent` function in `extensions/ql-vscode/src/view/common/SuggestBox/useEffectEvent.ts`:
    *   Accept a callback function `T` and return a stable function reference that maintains referential equality across re-renders.
    *   Ensure the stable function always invokes the most recent callback version.

*   Implement the `useOpenKey` function in `extensions/ql-vscode/src/view/common/SuggestBox/useOpenKey.ts`:
    *   Accept a `FloatingContext<RT>` and return an `ElementProps` object with the shape `{ reference: { onKeyDown: Function } }`.
    *   Ensure the `onKeyDown` handler:
        *   Calls `event.preventDefault()` and `context.onOpenChange(true, event)` when `key=' '`, `ctrlKey=true`, `altKey=false`, `metaKey=false`, `shiftKey=false`, and `context.open === false`.
        *   Does not call `context.onOpenChange` if `context.open === true`.
        *   Ignores other key combinations, including `Cmd+Space`, `Ctrl+Shift+Space`, `Ctrl+Alt+Space`, `Ctrl+Cmd+Space`, `Ctrl+Shift+Alt+Space`, plain Space, `Ctrl+Tab`, or `Ctrl+letter`.
    *   Maintain a stable `onKeyDown` function reference across re-renders using `useEffectEvent`.

*   Extract and share the mock object utility:
    *   Define `mockedObject` and `DeepPartial` in `extensions/ql-vscode/test/mocked-object.ts`.
        *   `mockedObject` should use a Proxy to return provided property values, throwing errors for unmocked properties except for specific exceptions.
        *   Ensure `Symbol.toStringTag` returns "MockedObject".
    *   Re-export `mockedObject` and `DeepPartial` from `extensions/ql-vscode/test/vscode-tests/utils/mocking.helpers.ts`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.