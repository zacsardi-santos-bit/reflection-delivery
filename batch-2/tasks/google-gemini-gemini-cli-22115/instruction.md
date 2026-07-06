Refactor the codebase to introduce a unified context interface for managing runtime dependencies in the agent CLI. Implement the AgentLoopContext interface to streamline access to the tool registry, message bus, AI client, configuration object, and session/prompt identifier as direct properties. Update relevant classes and methods to utilize this new interface.

*   Create a new interface `AgentLoopContext` in `packages/core/src/config/agent-loop-context.ts` with the following properties:
    *   `config`: Config
    *   `toolRegistry`: ToolRegistry
    *   `messageBus`: MessageBus
    *   `geminiClient`: GeminiClient
    *   `promptId`: string

*   Modify the `Config` class in `packages/core/src/config/config.ts` to:
    *   Implement `AgentLoopContext`.
    *   Expose `toolRegistry`, `messageBus`, `geminiClient`, `config`, and `promptId` as direct property getters.

*   Update the `ToolRegistry` class in `packages/core/src/tools/tool-registry.ts` to:
    *   Expose `messageBus` as a direct property accessor.

*   Modify the `CoreToolScheduler` class in `packages/core/src/core/coreToolScheduler.ts` to:
    *   Change the constructor options object to use `context` of type `AgentLoopContext`.
    *   Ensure all call sites pass `{ context: agentLoopContext, ... }`.

*   Update the `ConsecaSafetyChecker` class in `packages/core/src/safety/conseca/conseca.ts` to:
    *   Rename the `setConfig` method to `setContext`.
    *   Ensure it accepts an `AgentLoopContext` parameter.

*   Modify the `Scheduler` class in `packages/core/src/scheduler/` to:
    *   Accept a `context` parameter of type `AgentLoopContext` in the constructor.
    *   Ensure the `context` includes `config`, `messageBus`, and `toolRegistry`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.