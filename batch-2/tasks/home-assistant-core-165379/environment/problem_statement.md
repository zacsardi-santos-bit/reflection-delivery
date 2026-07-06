## Description

The OpenAI conversation integration in Home Assistant does not currently allow users to select a service tier when making API requests. Service tiers control cost, speed, and priority — for example, a "flex" tier offers lower cost but may occasionally be temporarily unavailable, while other tiers like "default" or "priority" offer different trade-offs. Different AI models support different subsets of these tiers, so the available choices should vary based on the selected model.

## Expected Behavior

- When configuring the integration, users should be able to choose a service tier from the options supported by their chosen model.
- The list of available service tier options should be filtered automatically: models that do not support the "flex" option should not display it, and similarly for the "priority" option. Models that support neither should show no service tier selector at all.
- The configured service tier should be stored and sent with each API request.
- If the "flex" tier is chosen and a request fails because that tier is temporarily unavailable, the integration should automatically fall back to the standard tier and retry the request transparently, rather than surfacing an error to the user.

## Why This Matters

Without service tier control, all users are locked into a single tier with no ability to optimize for cost or latency. More importantly, when using lower-cost tiers that can occasionally be unavailable, the current behavior is to fail the conversation entirely — a poor experience that could be avoided with an automatic retry at a different tier.
