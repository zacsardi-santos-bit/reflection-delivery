Implement a visual progress bar for MCP tool calls in the CLI, replacing the current text-based progress display. Ensure the progress bar can handle both determinate and indeterminate modes, and validate incoming progress data to maintain robustness.

*   Export a new `McpProgressIndicator` component from `packages/cli/src/ui/components/messages/ToolShared.tsx`.
    *   Accept props: `progress` (number, required), `total` (number, optional), `message` (string, optional), `barWidth` (number, required).
    *   Render a horizontal bar using block characters (█ for filled, ░ for empty) to the specified `barWidth`.
    *   Display a percentage label when `total` is provided and valid; otherwise, show the raw `progress` value.
    *   Include the `message` in the rendered output if provided.

*   Update the `ToolMessage` component in `packages/cli/src/ui/components/messages/ToolMessage.tsx`.
    *   Replace `progressPercent` prop with `progress` and `progressTotal`.
    *   Render `McpProgressIndicator` with `barWidth=20` when `status === CoreToolCallStatus.Executing` and `progress` is defined.
    *   Remove the old text-appended progress format.

*   Modify the `mapToDisplay` function in `packages/cli/src/ui/hooks/toolMapping.ts`.
    *   Map `call.progress` to `displayTool.progress` and `call.progressTotal` to `displayTool.progressTotal` for `ExecutingToolCall`.
    *   Set `progress` and `progressTotal` to `undefined` for non-Executing calls.

*   Extend the `ExecutingToolCall` type in `packages/core/src/scheduler/types.ts`.
    *   Add optional fields: `progress` and `progressTotal`.

*   Update the `IndividualToolCallDisplay` interface in `packages/cli/src/ui/types.ts`.
    *   Replace `progressPercent` with `progress` and `progressTotal`.

*   Enhance the `SchedulerStateManager`'s `updateStatus` method in `packages/core/src/scheduler/state-manager.ts`.
    *   Accept and store `progress`, `progressTotal`, `progressMessage`, and `progressPercent` when transitioning to Executing status.
    *   Preserve these fields on subsequent updates if not provided.

*   Refine the `handleMcpProgress` method in `packages/core/src/scheduler/scheduler.ts`.
    *   Only call `updateStatus` if the call is in Executing status.
    *   Compute `progressTotal` and `progressPercent` when `total` is valid.
    *   Stop handling progress events after `dispose()` is called.

*   Adjust the `emitMcpProgress` method in `packages/core/src/utils/events.ts` on `CoreEventEmitter`.
    *   Validate `progress` to ensure it is not NaN, negative, or Infinity before emitting.
    *   Emit valid payloads unchanged.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.