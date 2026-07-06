## Description

When configuring AI provider costs, there is currently no way to specify separate per-token rates for input tokens versus output tokens. The only option is a single unified cost value that gets applied uniformly to all token types. Since virtually all AI providers charge different rates for input and output tokens, this limitation makes cost estimates inaccurate.

## Expected Behavior

Users should be able to configure distinct per-token costs for inputs and outputs separately in provider configuration. Specifically:

- When both an input cost and an output cost are provided, they should take precedence over any single unified cost setting.
- When only one of the two is provided alongside a unified cost, the unified cost should serve as a fallback for the unspecified direction.
- This behavior should be consistent across all supported providers, including those with tiered pricing, prompt caching, audio tokens, and cloud-provider-specific pricing variants.

## Why This Matters

Most production AI providers publish distinct input and output pricing (e.g., input is cheaper than output). Without the ability to set them independently, any cost tracking or budgeting that relies on the tool's cost calculation will overcharge or undercharge in a provider-specific way, making it harder to accurately compare costs across providers or stay within budget.
