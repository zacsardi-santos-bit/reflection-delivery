## Description

The Codex client automatically injects environment context information into user messages before sending them to the API. This metadata (details about the working environment, current directory, etc.) is wrapped in special markup tags and silently prepended or appended to every user turn. While this behavior is useful for interactive sessions, it cannot currently be turned off.

Developers building integrations, running automated workflows, or operating in controlled environments often want clean messages — exactly what the user typed — without any automatically-appended context. There is no configuration option to suppress this injection.

## Expected Behavior

A new configuration flag should allow the environment context injection to be disabled. When this flag is set to off:

- User messages sent to the API must contain only the actual user content
- No environment context markup or metadata should appear in the user-role inputs of API requests

## Why This Matters

Without the ability to disable environment context injection, developers cannot fully control the exact content of messages sent to the API. This is a blocker for use cases that require precise, predictable API payloads — such as integration tests, deterministic prompt engineering, or privacy-sensitive deployments where system information should not be transmitted.
