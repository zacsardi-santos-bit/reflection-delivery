## Description

The session event pipeline currently handles oversized content by truncating it — cutting a long message short and appending a truncation marker. While this prevents individual fields from being too long, it does not fully control total event size, and truncated events still enter the pipeline and consume memory and bandwidth. Worse, the current approach breaks the parent-child chain of events when oversized events are later filtered out in a batch step: downstream events end up referencing the ID of a dropped event that no longer exists, leaving the chain dangling.

## Expected Behavior

- When an individual session event's estimated serialized size exceeds the allowed maximum, the **entire event** should be dropped and excluded from the output, rather than having its content trimmed.
- Each dropped event must be counted so callers can observe how many events were lost.
- When an event is dropped, the ordering chain must remain valid: the next kept event should link back to the last successfully kept event, not the dropped one. No dangling parent references.
- The maximum lengths used when storing user messages and assistant responses in the session database should be defined as shared named constants, exported from a single location, so the session reindexer and any other consumers always use the same bounds.

## Why This Matters

Without this fix, the event pipeline can produce chains with broken parent references whenever a large event is filtered after the fact. This corrupts the session history view and makes it impossible to correctly reconstruct the conversation flow. The drop-at-creation approach eliminates the problem at the source, and sharing size-limit constants prevents different parts of the system from silently diverging.
