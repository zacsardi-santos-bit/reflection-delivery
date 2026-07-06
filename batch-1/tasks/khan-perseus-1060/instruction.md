Redesign the Explanation widget to ensure accessibility and consistency across all contexts. Implement a unified button element with proper semantic and accessibility attributes, and manage content visibility through CSS. Update the graded-group widget's hint button labels to use more natural language.

*   Implement a single button element for the Explanation widget:
    *   Use `<button>` with `role="button"`, `type="button"`, `aria-disabled="false"`.
    *   Set `aria-expanded` to `"true"` when expanded and `"false"` when collapsed.
    *   Use `aria-controls` to reference the content container's ID.
    *   Set the accessible name to `showPrompt` when collapsed (default "Explanation") and `hidePrompt` when expanded (default "Hide explanation!").
*   Ensure the content container element:
    *   Has `data-test-id="content-container"`.
    *   Has an `id` matching the button's `aria-controls`.
    *   Uses `aria-hidden="true"` when collapsed and `aria-hidden="false"` when expanded.
    *   Contains `contentExpanded` in its CSS class when expanded, and `contentCollapsed` when collapsed.
    *   Does not contain `contentExpanded` when collapsed, and does not contain `contentCollapsed` when expanded.
    *   Includes `transitionExpanded` or `transitionCollapsed` in its class when the user has no preference for reduced motion and the widget is expanding or collapsing, respectively.
    *   Excludes `transitionExpanded` and `transitionCollapsed` when the user prefers reduced motion.
*   Enable interaction with the toggle button:
    *   Allow toggling via mouse click, Enter key, or Space bar.
    *   Update the button's accessible name, `aria-expanded`, and the content container's `aria-hidden` and CSS classes accordingly.
*   Update the graded-group widget's hint button labels:
    *   Use "Explain" as the accessible name when the hint is hidden.
    *   Use "Hide explanation" as the accessible name when the hint is shown.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.