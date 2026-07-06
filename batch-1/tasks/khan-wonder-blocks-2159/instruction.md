Implement a React class component named `FocusManager` to manage keyboard navigation for popover content. Ensure that focusable elements inside the popover are dynamically included or excluded from the tab order based on the user's navigation, maintaining correct circular navigation behavior.

*   Implement the `FocusManager` class component in `packages/wonder-blocks-popover/src/components/focus-manager.tsx`.
    *   Render a single container `div` with `ref`, `onFocus`, `onBlur`, and `onClick` event handlers.
    *   Use the `anchorElement` prop to identify the trigger element for the popover.

*   Manage tab order for internal focusable elements:
    *   On `focus` or `click` events on the container, set `tabIndex` of all internal focusable elements to "0".
    *   On `blur` event on the container, set `tabIndex` of all internal focusable elements to "-1".

*   Ensure correct circular keyboard navigation:
    *   Track the first and last focusable elements within the popover.
    *   When `Shift+Tab` is pressed on the first focusable element, move focus to the `anchorElement`.
    *   When `Tab` is pressed on the last focusable element, move focus to the next focusable element after the `anchorElement` in the document.

*   Handle popover opening and closing:
    *   On opening, shift focus immediately to the first focusable element inside the popover.
    *   Ensure that pressing `Tab` after opening moves focus to the next element after the trigger.
    *   Maintain correct forward and backward circular navigation with the popover open.

*   Implement lifecycle behaviors:
    *   On unmount, restore `tabIndex` of all internal focusable elements to "0" before returning focus to `anchorElement`.
    *   In `componentDidUpdate`, ensure event listeners are removed and re-added to prevent duplication.

*   Support optional `initialFocusId` prop to specify the initial focus target within the popover content.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.