## Description

When sending LLM requests through a gateway endpoint, token cost tracking is broken because the system doesn't know which underlying provider model the gateway endpoint maps to. This means that cost calculations return nothing useful for gateway-routed calls, even though the actual provider and model information is available in the gateway endpoint configuration.

Additionally, the cost lookup mechanism is too coarse: it only keys on model name, ignoring the provider. This causes ambiguity when multiple providers offer models with similar names.

Finally, there is no way to retrieve gateway endpoint details using the endpoint's human-readable name — only lookup by numeric endpoint ID is supported, which is inconvenient in many practical use cases.

## Expected Behavior

- When a token cost tracker is initialized for a gateway model, it should automatically look up the actual underlying provider and model from the gateway configuration and use that for cost calculations.
- The model cost lookup should use both the provider name and model name together as a compound key for more accurate matching.
- It should be possible to retrieve a gateway endpoint by either its ID or its human-readable name.
- The gateway endpoint lookup function should handle errors gracefully (e.g., when the endpoint is not found or the gateway store is unavailable) by returning a safe default rather than raising.

## Why This Matters

Cost tracking for AI pipelines that route through gateway endpoints is essential for monitoring and budgeting. Without the gateway model resolution, all gateway-routed calls appear to have no associated cost, making it impossible to accurately track spending for those calls.
