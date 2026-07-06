## Description

When running long-running agentic loops with certain Anthropic models, there is currently no way to configure a loop-wide token budget so the model can self-regulate its usage and wrap up gracefully as the budget depletes. Anthropic has a task budget feature (in beta) that lets you specify a total token budget — covering thinking, tool calls, tool results, and output — but the library has no support for passing this configuration through model settings.

## Expected Behavior

- Developers should be able to specify a token budget configuration in model settings, including a total budget and optionally a remaining budget for client-side budget tracking across multiple requests.
- When the budget is configured, the required API beta header should be enabled automatically — no manual header management needed.
- The budget configuration should compose naturally with existing effort settings; both should appear in the same output configuration object sent to the API.
- Using this feature on a model that does not support it should raise a clear, descriptive error.
- Specifying a remaining-budget value alongside server-side context compaction should be rejected early with a clear error, because the API itself rejects this combination. This validation should also apply when the message history already contains a compaction summary (which implicitly triggers server-side compaction), not only when compaction is explicitly configured.
- When the same sampling parameter appears in both the model settings and the extra body overrides, only a single deduplicated warning should be issued listing all unsupported parameters together.

## Why This Matters

Without task budget support, developers running agentic loops have no way to help the model pace itself across a full task. Manually managing the required beta headers is error-prone. And without pre-flight validation of incompatible configurations, developers get opaque API errors instead of actionable messages.
