Update the CLI to display context usage as "X% context used" instead of "X% context left". Provide feedback when conversation history is compressed, and ensure numeric settings display with units. Implement these changes in the relevant components and functions as specified.

*   Modify `ContextUsageDisplay` component:
    *   Display "X% context used" at standard terminal widths.
    *   Display only the percentage (e.g., "20%") without the label on narrow terminals.
    *   Show "0% context used" when no tokens are used.
    *   Show "100% context used" when the token limit is fully consumed.

*   Update context overflow warning messages:
    *   Format as "Sending this message (N tokens) might exceed the context window limit (M tokens left)."

*   Implement feedback for automatic history compression:
    *   In `useGeminiStream`, add an informational message with:
        *   Text: "Context compressed from X% to Y%."
        *   Secondary text: "Change threshold in /settings."
        *   Color: Use `theme.status.warning`.

*   Enhance `getDisplayValue` function:
    *   For percentage units, format as "raw value (percentage equivalent)".
    *   For other units, append the unit directly to the value.
    *   Append an asterisk if the setting is overridden from its default.

*   Ensure `HistoryItemInfo` type includes `secondaryText` field for informational messages.

*   Use `renderWithProviders` for component testing with specific terminal widths.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.