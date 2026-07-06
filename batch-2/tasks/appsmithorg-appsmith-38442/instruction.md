Refactor the inline text editing logic in the entity explorer by implementing a reusable hook within the design system. This hook should manage the editing lifecycle, including tracking the input value, validating it, and handling save or cancel actions based on user interactions.

*   Implement the `useEditableText` hook with the following specifications:
    *   Accept five parameters: `isEditing` (boolean), `name` (string), `exitEditing` (callback), `validateName` (function returning string or null), and `onNameSave` (callback).
    *   Return a 5-element tuple:
        *   Index 0: `inputRef` (React ref object for an HTMLInputElement, initially null).
        *   Index 1: `editableName` (string, initialized to the `name` parameter).
        *   Index 2: `validationError` (string or null, initialized to null).
        *   Index 3: Keyboard event handler.
        *   Index 4: Input change event handler.
    *   Update `editableName` and `validationError` when the change handler is called.
    *   Handle keyboard events:
        *   On Enter key:
            *   If the name hasn't changed, call `exitEditing` without calling `onNameSave`.
            *   If the name has changed and is valid, call `onNameSave` with the new name and `exitEditing`.
            *   If the name has changed and is invalid, call `exitEditing` without calling `onNameSave`.
        *   On Escape key, call `exitEditing` without calling `onNameSave`.
    *   On focusout event, if the name has changed to a valid value, call `onNameSave` and `exitEditing`.

*   Export `useEditableText` from the `@appsmith/ads` package:
    *   Ensure it is re-exported through the chain: `Editable/index.ts`, `EntityExplorer/index.ts`, and the top-level package index.

*   Update the `EditableName` component:
    *   Import `useEditableText` from `@appsmith/ads`.
    *   Delegate text-editing logic to the hook.
    *   Render an HTML input element with a textbox role when `isEditing` is true.

*   Update the `ListItem` component in the design system:
    *   Render list items with `role="listitem"` to ensure they are queryable by ARIA role.
    *   In the `DataSidePane`, ensure list items display the datasource name and description, or just the name if no description is available.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.