I'm working on a feature where restored agent session history can be missing tool calls that the agent actually performed.

*   When a worktree branch announcement is prepended to a restored session's first turn, the announcement must appear as the very first response part (a Markdown part at index 0) regardless of what the existing first response part is. If the existing first response part is already Markdown, the announcement text is prepended to that part's content; otherwise a new Markdown response part is created and inserted at index 0, pushing all prior parts back.

*   After inserting the branch announcement, the first turn's response parts must have: responseParts[0].kind === ResponsePartKind.Markdown (containing the branch name), and responseParts[1].kind === ResponsePartKind.ToolCall (the original tool call that was at position 0 before insertion).

*   When getMessages() replays session history and an assistant message contains tool requests, any tool request that does not have a corresponding lifecycle event pair (execution_start + execution_complete) must still be surfaced as a ToolCall response part with status ToolCallStatus.Completed.

*   For a tool request surfaced via fallback (no lifecycle events): the ToolCall part must have success=true and content=undefined.

*   For a tool request with a matching tool.execution_complete event that has success=false and an error message: the ToolCall part must have success=false and content set to [{type: ToolResultContentType.Text, text: <error.message>}].

*   Tool requests for hidden tools (such as intent-reporting tools, e.g. 'report_intent') must not be surfaced as ToolCall response parts during fallback history reconstruction.

*   When a tool request already has both a tool.execution_start and a tool.execution_complete lifecycle event that were processed via the normal event flow, no duplicate ToolCall response part may be emitted for that tool call ID.

*   The Markdown text content from an assistant message must appear as a Markdown response part before any fallback ToolCall parts generated from that message's tool requests.


*   Interface details: NO INTERFACES NEEDED

The tests exercise existing public methods (`getSessionMessages` on the agent and `getMessages` on the session object). No new symbols are introduced that the tests call by name. The required changes are purely behavioral modifications to internal logic within:

- `src/vs/platform/agentHost/node/copilot/copilotAgent.ts` — the function that prepends the worktree announcement to the first turn's response parts
- `src/vs/platform/agentHost/node/copilot/mapSessionEvents.ts` — the function that maps raw session events into structured turn data

The enums `ToolCallStatus` (with at least `Completed`) and `ToolCallConfirmationReason` (with at least `NotNeeded`) are imported from `src/vs/platform/agentHost/common/state/sessionState.ts` and must be exported from that module, but they already exist in the codebase and are not newly introduced by this change.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.