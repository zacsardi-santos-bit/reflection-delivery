## Description

The Accordion component in Gradio cannot be controlled or observed from outside the UI. There is currently no way for Python backend code to programmatically open or close an accordion, nor to react to its state changes in all scenarios. Additionally, when the accordion is hidden through the standard visibility setting, it incorrectly disappears from the DOM entirely rather than simply being visually hidden — which breaks layout behavior and consistency with other components.

## Expected Behavior

- The accordion should expose a way to read its current open/closed state programmatically.
- The accordion should expose a way to set its open/closed state programmatically (e.g. from Python backend code).
- When the state is changed programmatically from closed to open, the appropriate expand events must fire — just as if the user clicked the toggle button.
- When the state is changed programmatically from open to closed, the appropriate collapse event must fire — just as if the user clicked the toggle button.
- If the programmatic update sets the same state that already exists, no events should fire and the DOM should not change.
- The programmatic label update should also be supported (changing the displayed label text).
- When the component is made invisible via the visibility property (set to false), it should remain in the DOM and be hidden via a CSS class — not removed from the page.

## Why This Matters

Without programmatic control, developers cannot use the Accordion as an interactive, server-controlled UI element. The missing events also mean that event handlers registered from Python code would never be triggered when state is changed programmatically. The visibility bug causes layout inconsistencies compared to other Gradio components.
