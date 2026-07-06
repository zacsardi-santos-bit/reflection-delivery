Implement the "ShowMoreLines" component to manage its own spacing, ensuring it applies appropriate padding and margin. Update related components to remove redundant margin logic and ensure consistent spacing in different display modes.

*   Update the ShowMoreLines component:
    *   Apply `paddingX=1` to its container to create a 1-space indent before the text.
    *   Apply `marginBottom=1` to its container to ensure one empty line below the component.
    *   Render the text 'Press Ctrl+O to show more lines' when in alternate buffer mode with `constrainHeight={true}` and at least one overflowing item.
    *   Render no visible text content when not in alternate buffer mode, or when `constrainHeight={false}`, or when no items are overflowing.
*   Modify the GeminiMessage component:
    *   Remove the conditional `marginTop`/`marginBottom` logic from the Box wrapping ShowMoreLines.
    *   Use a plain `<Box>` with no margin props for wrapping ShowMoreLines.
*   Modify the GeminiMessageContent component:
    *   Remove the conditional `marginTop`/`marginBottom` logic from the Box wrapping ShowMoreLines.
    *   Use a plain `<Box>` with no margin props for wrapping ShowMoreLines.
*   Ensure MainContent renders:
    *   Multiple history items (e.g., two Gemini messages) in alternate buffer mode with `constrainHeight` enabled, separated by exactly one empty line.
    *   A mix of user and Gemini history items in alternate buffer mode with `constrainHeight` enabled, maintaining appropriate single-line spacing.
*   Ensure no visible output from ShowMoreLines in normal buffer mode and unconstrained-height contexts.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.