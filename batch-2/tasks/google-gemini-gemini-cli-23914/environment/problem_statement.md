## Description

When a subagent completes its work, its internal activity trace — the reasoning steps and tool invocations it performed — is currently lost. The conversation history only shows the final result, giving users no way to review what the subagent actually did during its execution. This makes it difficult to audit, debug, or understand subagent behavior after the fact.

## Expected Behavior

- Subagent activity (thoughts and tool calls) should be broadcast over the internal event bus as they occur, making the activity stream available to the UI in real time.
- A completed subagent run should leave behind a persistent history entry in the conversation that shows every thought and tool invocation, in order.
- The history display should clearly distinguish between reasoning activity (with a brain icon) and tool usage (with a tool icon), and show status indicators for each item: whether it completed successfully, is still running, or encountered an error.
- When the same activity is updated (e.g., a running thought transitions to completed), the existing entry should be updated in place rather than duplicated.

## Why This Matters

Users and developers working with subagents need to be able to review what happened after the fact — not just see the final result. Without a persistent trace, failures are hard to diagnose and successful runs are opaque. This change makes subagent behavior fully transparent and reviewable.
