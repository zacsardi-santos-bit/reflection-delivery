## Description

The logic for managing inline name editing in the entity explorer — tracking the current input value, validating it, saving on confirmation, and cancelling on escape — is currently duplicated inside individual components. This makes the behavior hard to test in isolation and impossible to reuse across different parts of the UI. We should extract this logic into a dedicated, reusable hook that lives in the design system and can be shared by any component that needs inline text editing.

## Expected Behavior

- A new composable hook in the design system manages the full inline-editing lifecycle: it tracks the editable name, calls an external validation function on each change, saves on Enter (if the name changed and is valid), cancels on Escape, and auto-saves when the input loses focus.
- The hook returns a reference to the input element, the current editable name, the current validation error, a keyboard event handler, and a change event handler — in that fixed order.
- If the name has not changed when the user confirms, the hook exits editing mode without calling the save callback.
- If the new name fails validation, the hook exits editing mode without saving.
- The hook is published as a named export from the design system package so any consumer can import it directly.
- The editable name component in the IDE is refactored to use this shared hook instead of its own duplicated logic.
- List items in the design system carry proper list item semantics so they can be reliably identified by their ARIA role.

## Why This Matters

Centralising the inline-editing logic in a single well-tested hook eliminates duplication, makes the behaviour verifiable in isolation, and allows future components to adopt editable names without re-implementing the same state management and event handling.
