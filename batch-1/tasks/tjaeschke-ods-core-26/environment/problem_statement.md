## Description

The webhook proxy currently requires callers to include a project identifier in every build trigger request payload. This is redundant and error-prone: the project can always be reliably derived from the namespace that the server is already configured with (by removing a standard suffix). Additionally, the internal event representation carries a project field that isn't needed there since the server already knows its own project context.

## Expected Behavior

- The server should be initialized with a dedicated project field derived once from its namespace configuration, rather than extracting the project from each incoming request.
- The build trigger request payload should not require or accept a project field — the server should use its own project context for all processing.
- Internal event objects should not carry a project field; events should be identified and validated based on their namespace, repository, component, branch, and pipeline fields.
- Event validity should be determined without checking for a project field on the event.

## Why This Matters

Requiring callers to pass a project in every request creates an opportunity for inconsistency — the project in the request might not match the server's actual project context. Centralizing the project at server initialization time makes the system more consistent and removes unnecessary data from both the API contract and the internal data model. Callers benefit from a simpler payload format, and the server behaves more predictably.
