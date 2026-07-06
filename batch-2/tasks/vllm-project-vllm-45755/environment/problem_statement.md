## Description

A new model family needs to be added to the engine-based streaming parser infrastructure. Currently, this model family is not supported by the token-aware engine parser path, so users cannot take advantage of precise special-token detection when running these models. Without support, streaming outputs for this model family are not correctly parsed into reasoning traces, tool calls, and content.

This model has a unique output behavior: when thinking mode is disabled — or when no meaningful content follows the thinking block — the reasoning and content fields must be **swapped** so the user receives the intended answer rather than an empty or misrouted response. This swap should be triggered by request-level configuration and should only apply when the content that follows the reasoning delimiter is absent or entirely whitespace.

Additionally, the parser must distinguish real tool invocations (signaled by special token IDs) from explanatory text that merely contains tool-call-like syntax within the reasoning section. Without this distinction, a model that explains tool usage in its thinking output would be incorrectly parsed as making live tool calls.

The existing standalone reasoning adapter for this model family should be migrated to the newer engine-based adapter system.

## Expected Behavior

- The new model's parser is registered in the engine adapter system and auto-discovered by replay tests
- Reasoning/content swap occurs when thinking mode is disabled and no real content follows the reasoning block
- Whitespace-only content after the reasoning delimiter also triggers the swap
- Swap does not occur when thinking mode is active, when real content is present, or when no relevant configuration is set
- Tool calls are parsed correctly using the same XML format used by existing models
- Parallel tool calls are supported
- Real tool calls (identified by special token IDs) are distinguished from tool-call syntax mentioned in reasoning text
- The replay test infrastructure automatically discovers all registered parsers without requiring manual hardcoding

## Why This Matters

Without this change, users running this model family cannot get correctly structured streaming responses — reasoning traces, tool calls, and regular content will not be parsed and delivered accurately. The swap behavior is critical for configurations where the model's thinking mode is turned off.
