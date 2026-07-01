Improve the accessibility of dropdown select components by updating their ARIA roles and implementing persistent accessible labels. Ensure that both single-selection and multi-selection dropdowns can accept and maintain a stable accessible label, even when custom openers are used.

*   Update the SelectOpener component:
    *   Render the interactive trigger element with the ARIA role 'combobox' instead of 'button'.
*   Modify the SingleSelect component:
    *   Accept an 'aria-label' prop and ensure the opener element (role='combobox') has an accessible name equal to the aria-label value.
    *   Maintain the accessible name as the aria-label value, regardless of the selected option.
    *   When using a custom opener, pass the aria-label to the custom opener unless it defines its own aria-label.
*   Modify the MultiSelect component:
    *   Accept an 'aria-label' prop and ensure the opener element (role='combobox') has an accessible name equal to the aria-label value.
    *   Maintain the accessible name as the aria-label value, regardless of the selection state.
    *   When using a custom opener, pass the aria-label to the custom opener unless it defines its own aria-label.
*   Update TypeScript types:
    *   Rename 'Labels' to 'LabelsValues' in the MultiSelect module.
    *   Rename 'SingleSelectLabels' to 'SingleSelectLabelsValues' in the SingleSelect module.
*   Ensure accessibility audits pass without violations when SingleSelect or MultiSelect is rendered with `opened={true}` and an aria-label is provided.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.