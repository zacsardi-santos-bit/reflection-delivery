## Description

State transition requests for flow runs can sometimes be submitted more than once — for example, when a network failure prevents a client from receiving the server's response and the client retries. Currently, the orchestration system has no mechanism to detect that an identical transition was already applied, so the retry gets processed as a brand-new request. This can cause unexpected behavior or inconsistent state.

## Expected Behavior

- Each state transition request should be able to carry a unique identifier that the server can use to detect retries.
- If the server detects that the current run state already carries the same unique identifier as the incoming request, it should reject the incoming transition as a duplicate and return the already-existing state, rather than applying the change again.
- If either the current state or the proposed state is missing the unique identifier, the transition should proceed normally.
- If both states carry identifiers but they differ, the transition should also proceed normally.
- The client should automatically generate and attach a fresh unique identifier to every state transition request it sends, making retries detectable without any manual effort by callers.
- Existing behavior for invalid transitions (such as a run that cannot move from one state to the same state type) should remain unchanged for the cases where duplicate detection does not apply.

## Why This Matters

Without this, clients cannot safely retry state transition requests. A retry that "succeeds" when the original already succeeded leads to ambiguous server-side state. With duplicate detection in place, clients can retry idempotently and the server will simply confirm the already-applied transition rather than applying it again.
