Update the Starlight Tailwind integration to ensure that the CSS generated for dark mode utility classes and the base CSS layer matches the current Tailwind CSS library's output. Adjust the selector pattern and include missing CSS custom properties as specified.

*   Ensure the generated base CSS layer includes the following CSS custom properties, each initialized to an empty string:
    *   `--tw-contain-size`
    *   `--tw-contain-layout`
    *   `--tw-contain-paint`
    *   `--tw-contain-style`
    *   These properties must be present in both the universal selector block and the `::backdrop` pseudo-element selector block.

*   Modify the dark mode utility variant class processing to generate the correct CSS selector format:
    *   The selector for a dark mode utility class (e.g., `dark:text-red-50`) must be structured as `.dark\:class-name:is([data-theme="dark"] *)`.
    *   Ensure that the utility class selector appears first, followed by `:is([data-theme="dark"] *)`.
    *   Eliminate the previous format `:is([data-theme="dark"] .dark\:class-name)` from the output.

*   Verify that the generated CSS for a dark mode utility class includes a selector string containing `.dark\:text-red-50:is([data-theme="dark"] *)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.