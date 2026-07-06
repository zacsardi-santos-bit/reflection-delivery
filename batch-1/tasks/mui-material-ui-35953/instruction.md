Fix the Joy UI components to ensure proper event handler forwarding and default tab panel visibility. Implement the necessary changes in the Input, Textarea, and Tabs components to meet the specified behavior.

*   Update the Input component in `packages/mui-joy/src/Input/Input.tsx`:
    *   Forward `onKeyDown` and `onKeyUp` event handlers from `slotProps.input` to the native input element.
    *   Forward `onFocus` and `onBlur` event handlers from `slotProps.input`, ensuring they override top-level handlers.
    *   Ensure the `placeholder` prop is rendered correctly on the native input element.

*   Update the Textarea component in `packages/mui-joy/src/Textarea/Textarea.tsx`:
    *   Forward `onKeyDown` and `onKeyUp` event handlers from `slotProps.textarea` to the native textarea element.
    *   Forward `onFocus` and `onBlur` event handlers from `slotProps.textarea`, ensuring they override top-level handlers.
    *   Ensure the `placeholder` prop is rendered correctly on the native textarea element.

*   Update the Tabs component system:
    *   Ensure that when Tabs, TabList, Tab, and TabPanel are composed, the first TabPanel (index 0) is visible by default.
    *   Ensure panels not associated with the active tab are hidden by default.
    *   Make the `value` prop on TabPanel optional and default to 0, ensuring the first panel is visible when no `value` is provided.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.