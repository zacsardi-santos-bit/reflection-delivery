Implement structured error handling for remote agent operations to provide user-friendly messages that guide users in troubleshooting configuration issues. Create a new module to define specific error types and a function to classify errors based on their characteristics.

*   Create a new module at `packages/core/src/agents/a2a-errors.ts` to export error classes and a classification function:
    *   Define `A2AAgentError` class extending `Error` with:
        *   `name` set to 'A2AAgentError'.
        *   `readonly agentName` and `readonly userMessage` properties.
        *   Constructor: `(agentName: string, message: string, userMessage: string)`.
    *   Define `AgentCardNotFoundError` class extending `A2AAgentError` with:
        *   `name` set to 'AgentCardNotFoundError'.
        *   `userMessage` containing '404', the `agentCardUrl`, and 'agent_card_url'.
        *   Constructor: `(agentName: string, agentCardUrl: string)`.
    *   Define `AgentCardAuthError` class extending `A2AAgentError` with:
        *   `name` set to 'AgentCardAuthError'.
        *   `readonly statusCode` property.
        *   `userMessage` containing the status code, 'Unauthorized' or 'Forbidden', and '"auth" configuration'.
        *   Constructor: `(agentName: string, agentCardUrl: string, statusCode: 401 | 403)`.
    *   Define `AgentAuthConfigMissingError` class extending `A2AAgentError` with:
        *   `name` set to 'AgentAuthConfigMissingError'.
        *   `readonly requiredAuth` and `readonly missingFields` properties.
        *   `userMessage` containing the requiredAuth description, 'no auth is configured', and 'Missing:'.
        *   Constructor: `(agentName: string, requiredAuth: string, missingFields: string[])`.
    *   Define `AgentConnectionError` class extending `A2AAgentError` with:
        *   `name` set to 'AgentConnectionError'.
        *   `userMessage` containing the cause message and `agentCardUrl`.
        *   Constructor: `(agentName: string, agentCardUrl: string, cause: unknown)`.
    *   Implement `classifyAgentError` function:
        *   Signature: `(agentName: string, agentCardUrl: string, error: unknown): A2AAgentError`.
        *   Traverse error cause chain, checking for network error codes first (e.g., ECONNREFUSED, ENOTFOUND).
        *   Return `AgentConnectionError` for network errors.
        *   Return `AgentCardNotFoundError` if '404' or 'not found' is detected.
        *   Return `AgentCardAuthError` with statusCode 401 for '401' or 'unauthorized'.
        *   Return `AgentCardAuthError` with statusCode 403 for '403' or 'forbidden'.
        *   Default to `AgentConnectionError` for other errors.

*   Update `A2AClientManager`:
    *   Ensure `sendMessageStream` propagates errors without additional prefixes.

*   Modify agent registration process:
    *   Emit error-level feedback as '[AgentName] <error.userMessage>' for `A2AAgentError`.
    *   Emit '[AgentName] Failed to load remote agent: <error.message>' for non-`A2AAgentError`.
    *   Emit warning-level feedback when security schemes are unmet but register the agent for fallback.

*   Enhance `A2AAuthProviderFactory`:
    *   Add `static validateAuthConfig` method to return validation results.
    *   Add `static describeRequiredAuth` method to describe required authentication schemes.

*   Update `RemoteAgentInvocation.execute()`:
    *   Set `error` and `returnDisplay` with `userMessage` for `A2AAgentError`.
    *   Use 'Error calling remote agent: <error.message>' for non-`A2AAgentError`.
    *   Include partial output and error message if an error occurs mid-stream.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.