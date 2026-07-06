## Description

There are two related issues with how subagent activity is handled in the CLI:

1. **Sensitive data leaks through activity streams.** When subagents run, sensitive information — such as passwords, API keys, authentication tokens, and cryptographic key material — can appear in error messages, tool call arguments, and the agent's own thought output. This data gets surfaced in progress displays and logs without any sanitization, which is a security concern.

2. **Thought content accumulates instead of updating.** As a subagent streams its reasoning, each new thought fragment is appended to the activity list rather than replacing the previous one. This causes the activity display to grow unboundedly with stale partial thoughts, when users really only care about the agent's current thinking.

## Expected Behavior

- A utility should exist for sanitizing agent-related content before it is displayed or logged. It should:
  - Remove PEM-encoded cryptographic material from error messages, replacing it with a safe placeholder.
  - Remove sensitive key-value pairs (like API keys and credentials) from error messages and tool arguments.
  - Remove token-like sensitive values from agent thought content.
  - Be resilient to adversarial inputs that could otherwise cause the sanitization logic to hang or time out.

- When a subagent emits a new thought, it should replace the previous thought in the activity list rather than adding another entry.

## Why This Matters

Users and operators relying on the activity stream or logs should not see credentials or private key material. Accumulating partial thoughts also makes the UI hard to read during long agent runs.
