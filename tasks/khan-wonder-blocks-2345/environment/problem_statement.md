## Description

Our dropdown select components currently expose their trigger button with a generic role that does not accurately convey to assistive technologies that these controls are selection widgets. Additionally, there is no way for developers to provide a persistent accessible label for the component — the control's accessible name currently reflects the currently selected value (or placeholder), which changes dynamically as users make selections. This means screen reader users may lose context about what a field is for once they've picked a value.

## Expected Behavior

- The trigger element for all select-style dropdown components should correctly identify itself as a combo box control to assistive technologies (rather than a generic button).
- Both the single-selection and multi-selection dropdown components should accept a developer-supplied accessible label.
- When an accessible label is provided, it should remain as the control's accessible name regardless of which option(s) are selected or deselected.
- When a custom opener element is used and the developer also supplies an accessible label on the component, the component-level label should be passed down to the custom opener. If the custom opener defines its own accessible label, that label takes priority.
- Accessibility audits should pass without violations when an accessible label is supplied.

## Why This Matters

Screen reader users rely on stable, descriptive names for form controls to understand what a field is for. When the accessible name changes dynamically with selection state, users lose the field's context after making a choice. Correctly identifying these controls as combo boxes also ensures that assistive technologies apply the right interaction model, improving overall keyboard and screen reader usability.
