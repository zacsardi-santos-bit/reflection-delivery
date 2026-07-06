## Description

When using promptfoo's Google integration through Vertex AI, cost information is never reported — cost is simply absent from every Vertex AI call response, even when the underlying model has publicly available pricing. This makes it impossible to track, compare, or budget AI costs when using the Vertex AI path.

Additionally, several newer Google AI models are missing from the pricing catalog, so cost estimates are unavailable even in AI Studio mode for those models.

## Expected Behavior

- Vertex AI calls should report actual costs based on token usage, using the same pricing catalog used for AI Studio calls.
- For models where Vertex AI pricing differs from AI Studio pricing, the Vertex-specific rate should be used.
- For models without a separate Vertex AI price, the standard pricing should apply as a fallback.
- The following models should have pricing data available: an embedding model and a robotics preview model that are currently missing from the catalog.
- A newer Pro preview model should support tiered pricing (higher rates above a token threshold).
- When a response does not include token usage data, cost should be reported as unavailable rather than zero or causing an error.
- When a cached response is returned, the cost and metadata from the original response should be preserved and included in the result.

## Why This Matters

Teams running evals or benchmarks on Vertex AI currently have no visibility into cost, making resource planning difficult. Fixing cost tracking for Vertex AI — and expanding the model pricing catalog — ensures that cost reporting works consistently regardless of which Google API surface is used.
