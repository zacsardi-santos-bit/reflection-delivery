## Description

When a session's conversation history becomes too long, the system automatically compacts (summarizes) it to free up context space. However, the compaction request is currently missing several important configuration fields that the regular conversation requests carry. Specifically, the tool definitions available to the model, whether parallel tool execution is allowed, the reasoning configuration, and the text formatting controls are all absent from the compaction request.

## Expected Behavior

- The compaction request should include the same tool definitions as the regular conversation requests
- The compaction request should include the same parallel tool execution setting as regular requests
- The compaction request should include the same reasoning configuration as regular requests
- The compaction request should include the same text control settings as regular requests

## Why This Matters

Without these settings, the model behavior during compaction can differ from its normal behavior — for example, it might not be aware of available tools, or it might apply different reasoning settings. This inconsistency can lead to lower quality summaries or unexpected behavior after compaction. Ensuring parity between regular requests and compaction requests means the model has the same context and configuration throughout the session lifecycle.
