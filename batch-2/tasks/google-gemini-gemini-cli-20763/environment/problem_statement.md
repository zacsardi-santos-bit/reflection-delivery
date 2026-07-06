## Description

The agent loop detection system currently terminates a session the moment any repetitive behavior is detected. This "one-strike" policy is too aggressive: it aborts sessions even in cases where the agent could self-correct if simply given a warning.

## Expected Behavior

- When repetitive behavior is first detected, the system should attempt a recovery by alerting the agent about the potential loop and continuing the conversation — rather than immediately stopping.
- If the agent continues to loop after this initial warning and recovery attempt, the session should then be forcibly terminated.
- The loop detection result should carry a numeric count of how many times looping has been detected (not just a yes/no signal), so callers can apply escalating responses: attempt recovery on the first detection, terminate on the second.
- A new method should be available to clear the active detection state (allowing a recovery turn to proceed) while preserving the running count of prior detections.
- The threshold at which the LLM-based loop analysis is triggered should be reduced so that longer-running sessions begin monitoring earlier.

## Why This Matters

Premature session terminations due to transient or false-positive loop signals waste user time and prevent legitimate task completion. A graduated two-strike approach gives agents one opportunity to recover gracefully before a hard stop is enforced.
