## Description

When an AI agent needs to perform multi-step workflows — calling several tools, looping over results, filtering and combining them — each tool invocation requires a separate round-trip to the language model. For orchestration-heavy tasks this is expensive in both latency and token usage.

We'd like to add a "code mode" option to the agent operator. When enabled, the model writes a single block of code that calls the tools as functions (with loops, conditionals, and parallel calls) rather than issuing one tool call per round-trip. This should reduce round-trips and token consumption for multi-step agentic workflows.

## Expected Behavior

- The agent operator accepts a new boolean option (default off) to enable code mode.
- When disabled (default), the existing behavior is unchanged — no new fields are injected into agent creation.
- When enabled, a code-execution capability is built and appended to the agent's capability list at execution time. Any capabilities already supplied by the caller are preserved; the code-mode capability is added at the end.
- If the caller specifies both code mode and durable/replay mode simultaneously, the operator must reject the combination at construction time with a clear error, since durable mode requires a stable step order that code mode cannot guarantee.
- The code-mode capability must be constructed lazily — only when the agent actually runs — so the operator remains serialization-safe.
- If the optional package required by code mode is not installed, calling the capability builder must raise a clear optional-feature error referencing the required extra. If the package is present but a transitive dependency is broken, the original error must be re-raised rather than replaced with a misleading "install the extra" message.
- Tool definitions in the hook and SQL toolsets must declare their return type as a string, so that the code-execution environment can render correctly typed function signatures when generating code that calls those tools.

## Supporting Utility

- A small version-compatibility helper is needed that checks at import time whether the installed AI library version supports a return schema field on tool definitions. This helper must expose a boolean flag and a function that either returns the appropriate keyword argument or an empty dict when the feature is not available.

## Why This Matters

Without code mode, a workflow that calls 10 tools makes 10 separate model round-trips. With code mode, the model writes one block of code and executes all 10 tool calls in a single turn, saving latency and cost for orchestration-heavy tasks.
