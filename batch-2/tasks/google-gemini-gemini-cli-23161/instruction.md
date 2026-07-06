Implement dynamic synchronization of the tool registry with agent configuration changes during runtime. Ensure that enabling or disabling agents updates the tool registry without requiring a system restart.

*   Update the tool registry based on agent configuration:
    *   When Config is initialized with agents enabled globally and a specific agent's override set to enabled, register the agent as a SubagentTool instance in the tool registry, using the agent name as the tool name.
    *   When Config is initialized with agents enabled globally but a specific agent's override explicitly set to disabled, ensure the tool registry does not contain that agent. `getAllToolNames()` must not include the agent name, and `getTool(agentName)` must return undefined.
    *   Ensure subagents are registered in the tool registry even if their name is absent from the allowedTools list. The allowedTools filter does not apply to subagent tools.

*   Implement the `onAgentsRefreshed` method in `packages/core/src/config/config.ts`:
    *   Define `onAgentsRefreshed(): Promise<void>` as a private async method in the Config class.
    *   When called, read the current agent settings using `getAgentsSettings()`.
    *   Update the tool registry: unregister any subagent tool whose override is set to disabled, and register any enabled agent not yet registered as a SubagentTool.

*   Ensure correct tool registry updates after `onAgentsRefreshed` is called:
    *   If an agent was previously registered but is now disabled, `getAllToolNames()` must not contain the agent name, and `getTool(agentName)` must return undefined.
    *   If an agent was previously unregistered but is now enabled, `getAllToolNames()` must include the agent name.

*   Enhance the AgentRegistry class in `packages/core/src/agents/registry.ts`:
    *   Implement `getAllDiscoveredAgentNames(): string[]` to return an array of all discovered agent name strings, regardless of their current enabled or loaded status. This method will help determine which agent tools to evaluate during a tool registry refresh.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.