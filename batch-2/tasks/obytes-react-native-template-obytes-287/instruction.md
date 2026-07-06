Implement dark mode support for the Button component's text color in the default (primary) variant. Ensure that the button label text is legible in both light and dark themes, particularly for the large-size variant.

*   Update the Button component to support dark mode text color for the default (primary) variant.
    *   Ensure that in dark mode, the label text color changes from white to black.
    *   Maintain the current white text color in light mode.
*   Modify the Button component for the large size ('lg') variant.
    *   Ensure the label element's class string includes 'font-[600] font-jakarta text-white dark:text-black text-xl'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.