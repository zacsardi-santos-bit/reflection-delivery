Implement a reusable hook for inline text editing and update the IDE's data source list for accessibility. Extract the inline name-editing logic into a standalone hook within the design system package, and ensure that data source entries are rendered with proper semantic roles.

*   Implement the `useEditableText` hook in `app/client/packages/design-system/ads/src/Templates/EntityExplorer/Editable/useEditableText.ts`:
    *   Accept parameters: `isEditing` (boolean), `name` (string), `exitEditing` (function), `validateName` (function returning string or null), `onNameSave` (function).
    *   Return a tuple: `[inputRef, editableName, validationError, handleKeyUp, handleTitleChange]`.
        *   `inputRef`: React.RefObject<HTMLInputElement>, initially `.current === null`.
        *   `editableName`: string, initialized to `name`.
        *   `validationError`: string | null, initially null.
        *   `handleKeyUp`: function handling Enter and Escape key events.
        *   `handleTitleChange`: function updating `editableName` and `validationError`.

*   Ensure `useEditableText` hook behavior:
    *   `handleTitleChange(event)`: Update `editableName` to `event.target.value` and re-run `validateName`.
    *   `handleKeyUp` with key "Enter":
        *   If `editableName` is unchanged, call `exitEditing()` without calling `onNameSave`.
        *   If `editableName` changed and `validateName` returns null, call `onNameSave(editableName)` and `exitEditing()`.
        *   If `editableName` changed and `validateName` returns non-null, call `exitEditing()` without calling `onNameSave`.
    *   `handleKeyUp` with key "Escape": Call `exitEditing()` without calling `onNameSave`.
    *   On `focusOut` event when `editableName` changed and valid, call `onNameSave(editableName)` and `exitEditing()`.

*   Export `useEditableText` from `@appsmith/ads` package for external use.

*   Refactor the `EditableName` component to use `useEditableText` for managing editing state.

*   Update the `ListItem` component in `app/client/packages/design-system/ads/src/List/List.tsx`:
    *   Render the root element with `role="listitem"` for accessibility.

*   Ensure each datasource entry in `DataSidePane`:
    *   Contains both the datasource name and usage description.
    *   Is rendered in a consistent, sorted order.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.