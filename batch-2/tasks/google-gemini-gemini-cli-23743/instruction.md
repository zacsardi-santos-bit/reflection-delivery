Implement the ability to specify a remote agent's card definition as an inline JSON string in the agent definition file. Ensure the system can handle both URL-based and JSON-based definitions, validate the JSON, and manage potential conflicts between the two methods.

*   Update the `AgentCardLoadOptions` type in `packages/core/src/agents/types.ts` to be a discriminated union:
    *   `{ type: 'url'; url: string } | { type: 'json'; json: string }`.

*   Modify the `RemoteAgentDefinition` interface in `packages/core/src/agents/types.ts`:
    *   Include `agentCardUrl?: string` and `agentCardJson?: string` as optional fields.

*   Implement the `getAgentCardLoadOptions` function in `packages/core/src/agents/types.ts`:
    *   Return `{ type: 'json', json: agentCardJson }` if `agentCardJson` is present.
    *   Return `{ type: 'url', url: agentCardUrl }` if `agentCardUrl` is present.
    *   Throw an error with message `/Remote agent '${def.name}' has neither agentCardUrl nor agentCardJson/` if neither is present.

*   Implement the `getRemoteAgentTargetUrl` function in `packages/core/src/agents/types.ts`:
    *   Return `agentCardUrl` if present.
    *   Parse `agentCardJson` and return the top-level 'url' field if it exists.
    *   Return `undefined` if the JSON is invalid, has no 'url' field, or neither field is present.

*   Update the `A2AClientManager.loadAgent` method in `packages/core/src/agents/a2a-client-manager.ts`:
    *   Change the second parameter to `options: AgentCardLoadOptions`.
    *   Parse JSON directly when `options.type === 'json'`.
    *   Preserve existing behavior for `options.type === 'url'`.
    *   Throw an error with message `/Failed to parse inline agent card JSON for agent '${name}'/` if JSON is invalid.

*   Ensure `loadAgent` logs 'inline JSON' in debug messages when loading from JSON.

*   Update `parseAgentMarkdown` in `packages/core/src/agents/agentLoader.ts` to support `agent_card_json`:
    *   Infer `kind: 'remote'` when `agent_card_json` is present.
    *   Include `agent_card_json` in the returned object.
    *   Reject with `/Validation failed/` if both `agent_card_json` and `agent_card_url` are present.
    *   Reject with `/agent_card_json must be valid JSON/` if JSON is invalid.

*   Update `markdownToAgentDefinition` in `packages/core/src/agents/agentLoader.ts`:
    *   Set `agentCardJson` when `agent_card_json` is present and `agentCardUrl` is undefined.
    *   Throw an error `/neither agent_card_json nor agent_card_url/` if neither field is present.

*   Ensure all code paths using `remoteDef.agentCardUrl` are updated to use `getAgentCardLoadOptions(remoteDef)`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.