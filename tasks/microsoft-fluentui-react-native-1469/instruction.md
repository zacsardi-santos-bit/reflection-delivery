Implement the necessary updates to the Checkbox component to address rendering stability issues, add missing design tokens, and ensure correct handling of accessibility actions. Ensure the component reuses style objects across renders and merges custom accessibility actions with the default toggle action.

*   Update the Checkbox component in `packages/experimental/Checkbox/src/Checkbox.tsx`:
    *   Implement support for the 'checkboxSize' design token to control the width and height of the checkbox box element.
    *   Implement support for the 'checkmarkSize' design token to control the width and height of the checkmark indicator.
    *   Implement support for the 'spacingLabelBefore' design token to control the marginEnd spacing of the label slot.
    *   Ensure the component renders with stable style objects across multiple renders when props are unchanged.
    *   Ensure the component renders identically on re-render with the same props.

*   Update the `Checkbox.compose()` method:
    *   Ensure it renders with default accessibility attributes, including accessibilityRole='checkbox', and accessibilityActions containing a 'Toggle' entry.
    *   Merge custom accessibility actions with the default toggle action.

*   Update the styling configuration in `packages/experimental/Checkbox/src/Checkbox.styling.ts`:
    *   Apply border-related styles (borderColor, borderRadius, borderWidth) after padding and paddingHorizontal in the root slot's style.

*   Update the `useCheckbox` hook in `packages/experimental/Checkbox/src/useCheckbox.ts`:
    *   Memoize the accessibilityState object to ensure identical inputs return the same object reference across renders.
    *   Use a stable reference for the default accessibilityActions array to maintain referential stability.

*   Ensure that rendering the Checkbox component with a 'style' prop and re-rendering with the same style object reference produces stable computed styles.

*   Ensure that rendering the Checkbox component with an 'accessibilityActions' prop and re-rendering with the same prop value produces identical output, with the default Toggle action always present.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.