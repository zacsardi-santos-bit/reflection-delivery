Implement a persistent activity trace for subagents in the CLI, ensuring that all reasoning steps and tool invocations are preserved in the conversation history. Broadcast subagent activities over the internal event bus in real-time and update the UI to display these activities with appropriate icons and status indicators.

*   Extend the `HistoryItemBase` type in `packages/cli/src/ui/types.ts`:
    *   Add a new type `HistoryItemSubagent` with fields: `type` ('subagent' literal), `agentName` (string), and `history` (array of `SubagentActivityItem`).
    *   Include `HistoryItemSubagent` in the `HistoryItemWithoutId` union type.

*   Update `packages/core/src/confirmation-bus/types.ts`:
    *   Add `SUBAGENT_ACTIVITY` to the `MessageBusType` enum with the string value 'subagent-activity'.
    *   Create a `SubagentActivityMessage` interface with fields: `type` (`MessageBusType.SUBAGENT_ACTIVITY`), `subagentName` (string), and `activity` (`SubagentActivityItem`).
    *   Add `SubagentActivityMessage` to the `Message` union type.

*   Implement the `SubagentHistoryMessage` component in `packages/cli/src/ui/components/messages/SubagentHistoryMessage.tsx`:
    *   Render a header with the agent name, 'Trace', and item count, prefixed by the robot emoji (🤖).
    *   Display activities with type 'thought' using the brain emoji (🧠) and 'tool_call' using the wrench emoji (🛠️).
    *   Append status indicators: 'running' with ' (Running...)', 'completed' with ' ✅', and 'error' with ' ❌'.

*   Modify the `useToolScheduler` hook in `packages/cli/src/ui/hooks/useToolScheduler.ts`:
    *   Subscribe to `SUBAGENT_ACTIVITY` messages on the message bus.
    *   Accumulate activities keyed by `subagentName`, upserting by `id`.
    *   Ensure `subagentHistory` is undefined before receiving events and populated with activities after receiving them.

*   Update `LocalSubagentInvocation` to handle `THOUGHT_CHUNK` events:
    *   Publish a `SUBAGENT_ACTIVITY` message with type 'thought' and content from `data.text`.
    *   Use `displayName` for `subagentName`, defaulting to `name` if absent.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.