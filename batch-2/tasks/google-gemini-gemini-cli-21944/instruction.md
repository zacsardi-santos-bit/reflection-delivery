Refactor the execution context in the core scheduling pipeline to consolidate configuration, tool registry, message bus, and prompt identity into a single unified context interface. Update the scheduler, tool executor, and policy components to utilize this unified context instead of separate arguments.

*   Update the `AgentLoopContext` interface in `packages/core/src/config/agent-loop-context.ts`:
    *   Add a new readonly `config` field of type `Config`.
    *   Ensure it includes the following properties: `config` (Config), `promptId` (string getter), `toolRegistry` (ToolRegistry getter), and `messageBus` (MessageBus or undefined).

*   Modify the `Config` class in `packages/core/src/config/config.ts`:
    *   Implement the updated `AgentLoopContext` interface.
    *   Add a `get config(): Config` getter that returns `this`.

*   Update the `GeminiClient` class in `packages/core/src/core/client.ts`:
    *   Change the constructor to accept an `AgentLoopContext` instead of a plain `Config`.
    *   Obtain the internal config reference via `context.config`.

*   Modify the `SchedulerOptions` interface in `packages/core/src/scheduler/scheduler.ts`:
    *   Rename the `config: Config` field to `context: AgentLoopContext`.
    *   Ensure the `messageBus` field remains optional.

*   Update the `Scheduler` class in `packages/core/src/scheduler/scheduler.ts`:
    *   Set the internal context from `options.context`.
    *   Derive config from `context.config`.
    *   Resolve `messageBus` as `options.messageBus ?? context.messageBus`.

*   Refactor the `ToolExecutor` class in `packages/core/src/scheduler/tool-executor.ts`:
    *   Change the constructor to accept a single `AgentLoopContext` parameter.
    *   Remove the separate config parameter and access config via a private getter that reads `context.config`.

*   Update the `updatePolicy` function in `packages/core/src/scheduler/policy.ts`:
    *   Change the 4th parameter from `{ config, messageBus, toolInvocation? }` to an `AgentLoopContext`.
    *   Add a new optional 5th parameter for `AnyToolInvocation`.

*   Ensure the `toolRegistry` is accessible as a getter property:
    *   Implement this on `AgentLoopContext` and `Config`.
    *   Replace any `getToolRegistry()` method calls with direct property access.

*   Ensure the `promptId` is accessible as a getter property:
    *   Implement this on `AgentLoopContext` and `Config`.
    *   Replace any AsyncLocalStorage context store retrievals with direct property access.

*   When creating a sub-agent `Scheduler`, ensure:
    *   Pass the agent-specific context as the `context` field in `SchedulerOptions`.
    *   The context's `toolRegistry` property reflects the overridden agent-specific tool registry.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.