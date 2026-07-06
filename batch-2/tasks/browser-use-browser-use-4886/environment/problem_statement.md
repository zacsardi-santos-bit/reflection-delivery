## Description

When using AI agents routed through OpenRouter, token cost tracking doesn't work correctly. The system lacks any support for looking up OpenRouter's own pricing data, which means models available exclusively through OpenRouter (or newly added ones not yet in the local pricing database) return no cost information at all.

There are two related issues:

1. **No OpenRouter pricing lookup**: There is no way to fetch and convert OpenRouter's published per-token pricing into the format the cost tracking system uses. This means any model accessed via OpenRouter that isn't in the local pricing file simply returns no cost data.

2. **No fallback to OpenRouter when local pricing is missing**: When the system cannot find pricing for a model in its local database, it should automatically try fetching pricing from OpenRouter. Currently, it just gives up and returns nothing.

3. **No cache-aware cost splitting for OpenRouter models**: OpenRouter publishes separate prices for cache-read tokens versus fresh prompt tokens, but the cost calculation doesn't use these distinct rates, so cached-token costs are reported incorrectly.

4. **Registered OpenRouter LLMs use wrong pricing source**: When a user registers an OpenRouter-backed model with the cost tracker, subsequent cost calculations should pull pricing from OpenRouter's API (with the appropriate provider prefix), not from the local pricing database — even if a same-named entry happens to exist locally.

## Expected Behavior

- A utility that converts OpenRouter's metadata format into the internal pricing structure, supporting all cost fields including cache read and cache write pricing.
- Model identifiers carrying a provider prefix should be handled transparently — the prefix should be stripped for lookup but preserved in the returned pricing record.
- The cost tracker should fall back to OpenRouter's pricing API when a model is not found locally.
- When a user registers an OpenRouter-backed LLM with the cost tracker, that model's costs must always be sourced from OpenRouter's API with the correct prefixed identifier, overriding any local data.

## Why This Matters

Without this, users running AI agents through OpenRouter have no reliable cost tracking, and cached token savings are not correctly reflected in cost reports.
