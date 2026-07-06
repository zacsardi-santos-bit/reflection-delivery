Implement a feature in the Floating Action Button (FAB) component to allow developers to disable shadow rendering by setting the shadow token to an absent value. Ensure that when the shadow is disabled, the button renders without any additional shadow wrapper views, maintaining all other button properties and styles.

*   Update the FAB component to accept an absent (undefined) value for the shadow token in its customization API.
    *   Ensure that when the shadow token is absent, the component does not render any shadow wrapper view.
*   Maintain all standard button properties and styles when the shadow is disabled:
    *   Accessibility attributes
    *   Interactive event handlers
    *   Visual styles such as background color, border color, border radius, dimensions, padding, and text styling.
*   Ensure the rendered structure of a shadow-free FAB matches the expected output:
    *   A single accessible button View containing a Text child.
    *   No additional wrapping views for shadow.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.