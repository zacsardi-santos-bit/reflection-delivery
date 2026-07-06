Implement an expandable topic message display in the CLI that allows users to view full summaries of topics. Ensure the display adapts based on terminal constraints and user interactions.

*   Update the `TopicMessage` component in `packages/cli/src/ui/components/messages/TopicMessage.tsx`:
    *   Accept an optional `availableTerminalHeight` prop (number).
        *   If undefined, render both strategic intent and summary text.
        *   If defined and item is not expanded, render only the title and strategic intent.
    *   Utilize `ToolActionsContext` to manage expansion state:
        *   Use `isExpanded(callId: string) => boolean` to determine if the summary should be rendered.
        *   On click, call `toggleExpansion(callId: string) => void` to toggle the expanded state.
    *   Handle edge cases in rendering:
        *   If `args` contains a summary but no strategic intent, display the summary as the primary description without expand/collapse control.
        *   If `args` contains a strategic intent but no summary, display only the strategic intent without expand/collapse control.
    *   Ensure the rendered output includes the title followed by a colon when intent or summary content is present.

*   Export the `TOPIC_PARAM_SUMMARY` constant from the `@google/gemini-cli-core` package:
    *   Use it as the key to access the summary value from a topic `args` object.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.