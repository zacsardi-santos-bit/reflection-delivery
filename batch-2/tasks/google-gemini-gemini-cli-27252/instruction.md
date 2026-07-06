Implement automatic handling of tool approval requests from subagents using a policy engine. Ensure that the session subscribes to these requests on startup and processes them through the policy engine to determine whether to approve, deny, or escalate to the user. Secure the message bus by stripping spoofable metadata fields from requests before forwarding them.

*   Update the `Config` class in `packages/core/src/config/config.ts`:
    *   Implement `getMessageBus()` to return a message bus object with `publish`, `subscribe`, and `unsubscribe` methods.
    *   Implement `getPolicyEngine()` to return a policy engine object with a `check` method or `undefined` if no engine is configured.

*   During session initialization:
    *   Obtain the message bus via `config.getMessageBus()`.
    *   Subscribe to `MessageBusType.TOOL_CONFIRMATION_REQUEST` messages with a handler that processes requests through the policy engine.

*   Implement the tool confirmation handler:
    *   On `PolicyDecision.ALLOW`, publish a `TOOL_CONFIRMATION_RESPONSE` with `{ confirmed: true, requiresUserConfirmation: false }`.
    *   On `PolicyDecision.ASK_USER`, publish a `TOOL_CONFIRMATION_RESPONSE` with `{ confirmed: false, requiresUserConfirmation: true }`.
    *   On `PolicyDecision.DENY`, publish a `TOOL_CONFIRMATION_RESPONSE` with `{ confirmed: false, requiresUserConfirmation: false }`.
    *   Look up tool metadata from the registry using the trimmed tool name, ensuring registry-sourced `toolAnnotations` and `serverName` (if applicable) are used.
    *   Pass the `subagent` field from the request to `policyEngine.check()`, ignoring incoming `serverName` and `toolAnnotations` fields.
    *   Fail safely by publishing `{ confirmed: false, requiresUserConfirmation: false }` if:
        *   The tool name is empty or whitespace-only.
        *   The tool is not found in the registry.
        *   `getPolicyEngine()` returns `undefined`.
        *   `policyEngine.check()` throws an exception.

*   Update the `MessageBus` class in `packages/core/src/confirmation-bus/message-bus.ts`:
    *   Implement `derive(subagentName: string)` to return a derived bus.
    *   Ensure `TOOL_CONFIRMATION_REQUEST` messages published through a derived bus have `forcedDecision`, `serverName`, `toolAnnotations`, and `details` stripped.
    *   Overwrite the `subagent` field to `<subagentName>/<original_subagent>` to enforce identity.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.