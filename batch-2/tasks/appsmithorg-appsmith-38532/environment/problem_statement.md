## Description

The inline text editing behavior in the entity explorer is currently embedded directly inside a UI component, making it difficult to test in isolation or reuse elsewhere. The logic for handling keyboard shortcuts, validation, and deciding whether to save or discard a name change should be extracted into a standalone, reusable hook that lives in the design system package.

Additionally, the data source list in the IDE's left panel does not render entries with proper semantic list item roles, which makes it harder to reliably query items by position and harder for accessibility tools to interpret the list structure.

## Expected Behavior

- A standalone, reusable hook should exist in the design system package that manages the full lifecycle of inline name editing: tracking the current text value, running name validation, handling Enter and Escape keyboard shortcuts, and responding to focus loss.
- When the user presses Enter with a valid name change, the save callback should be invoked and editing should exit.
- When the user presses Enter with an invalid name, editing should exit without saving.
- When the user presses Escape, editing should exit without saving regardless of the current name.
- When the input loses focus with a valid name change, the save callback should be invoked and editing should exit.
- When the name is unchanged and Enter is pressed, editing should exit without saving.
- The hook should be part of the design system's public export surface so components outside the design system can use it.
- The component that handles editable names in the IDE should delegate its editing logic to this shared hook.
- Each datasource entry in the data source list panel should be rendered as a proper semantic list item so the list structure is predictable and accessible.

## Why This Matters

Extracting the editing logic into a dedicated hook improves testability and reusability across the application. Teams working on different parts of the IDE can rely on a single, well-tested implementation of inline editing behavior rather than duplicating logic. Proper list item semantics for the data source panel also improve accessibility and make automated testing more reliable.
