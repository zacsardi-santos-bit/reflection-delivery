## Description

The Claude Code session management layer has an overly verbose and redundant API surface. When creating or invoking a session, callers are forced to supply low-level configuration details — such as server connectivity settings, model selection, and permission modes — even though this information is already tracked by shared service layers and could be looked up internally. This redundancy creates unnecessary coupling between callers and internal session implementation details.

## Expected Behavior

- The session object should be constructable with only the essential identity information: which language model server to use, what the session ID is, and whether this is a new or resumed session.
- Server connectivity details (port and authentication credentials) should be retrieved internally from the language model server at the time they are needed, not passed as constructor arguments.
- Model ID and permission mode should be read from the session state service at request time, not passed as constructor arguments.
- The prompt content should be resolved internally from the incoming chat request object rather than being pre-resolved and passed in as a separate argument.
- The agent manager's request-handling method should not require a chat context argument, since it does not use it.
- The agent manager's response should not include the session ID — callers already know which session they requested.

## Why This Matters

Forcing callers to pre-supply configuration that the session can retrieve itself leads to fragile, over-specified call sites that must be updated whenever internal session configuration changes. Removing these redundant parameters simplifies the API, reduces coupling, and makes session creation and invocation easier to work with correctly.
