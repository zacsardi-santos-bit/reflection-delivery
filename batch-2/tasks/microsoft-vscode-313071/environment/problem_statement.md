## Description

When a user switches between AI assistant modes (for example, from a full implementation/agent mode to a planning mode), the system should not attempt to reuse cached conversation state from the previous mode. Currently, mode switches are not properly accounted for, and the system carries over a "resume point" from the old mode into the new one. This can cause context confusion because different modes have different tools and behavior — the old cached state is no longer valid for the new mode.

## Expected Behavior

- When a mode change is detected, the AI request should start fresh: all relevant conversation messages should be sent rather than resuming from a stale cache marker.
- The cached marker message itself should be removed from the message list sent to the model.
- This behavior should apply consistently for both persistent connection (WebSocket) and regular HTTP requests.
- When no mode change has occurred (follow-up requests within the same mode), existing behavior should be preserved — caching and resumption from the last marker should continue to work.
- Multi-step mode switches (e.g., agent → plan → agent) should each independently force a fresh start on each transition.

## Planning Mode Tools

The planning mode agent should expose only read-only tools by default. File editing tools such as creating or modifying files must not be included in the default tool set for planning sessions. The exact set of allowed tools in planning mode should be derivable from a clearly named constant.

## Why This Matters

Without this fix, switching modes can cause the AI to respond with stale context from the previous session, leading to incoherent or incorrect answers in the new mode. Making the default tool restriction explicit for planning mode also prevents accidental write operations during what should be a read-only planning phase.
