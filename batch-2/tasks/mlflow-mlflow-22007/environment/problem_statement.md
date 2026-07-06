## Description

The judge evaluation system currently has a hard dependency on an optional third-party library for communicating with AI providers. When that library is not installed, users can only access a very limited set of providers and — critically — cannot use execution traces as evaluation context at all. This blocks an important use case: evaluating AI responses against real, observed application behavior without having to install additional dependencies.

## Expected Behavior

- A built-in fallback should allow judges to call major AI providers (OpenAI, Anthropic, Gemini, Mistral, and gateway endpoints) without needing the optional library.
- Trace-based evaluation — where the judge receives a recorded execution trace to reason about — should work even when the optional library is absent. Previously this raised an error.
- The fallback should support multi-turn agentic evaluation loops where the judge iteratively fetches trace data via tool calls.
- Context window overflows during tool-calling loops should be handled gracefully by pruning the oldest tool interactions and retrying.
- Proactive pruning should also happen before hitting the limit, when token usage exceeds a configurable threshold.
- Structured output formatting should degrade gracefully: if a model does not support it, the system retries without it rather than failing.
- Gateway and MLflow-managed endpoints should be routable through the internal gateway infrastructure, with proper caller identification.

## Why This Matters

Teams that do not want to install optional third-party packages should still get full judge functionality, including the ability to evaluate complex multi-step agent behavior captured in traces. The current hard failure when traces are used without the optional library is a significant usability gap.
