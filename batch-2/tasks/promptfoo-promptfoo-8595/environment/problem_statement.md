## Description

The promptfoo evaluation framework supports many AI model providers out of the box, but it currently has no built-in support for Abliteration AI — a service that offers models trained to remove refusal behaviors. Users who want to run evaluations against Abliteration models must resort to awkward workarounds using generic HTTP provider configurations.

We should add a first-class Abliteration provider that users can reference directly in their promptfoo configurations, just like they reference OpenAI, Anthropic, or any other supported provider.

## Expected Behavior

- Users should be able to reference Abliteration models by name using a standard prefix format, with an optional "chat" alias variant also supported
- The provider should authenticate via its own dedicated API key environment variable, completely separate from any OpenAI credentials — OpenAI API keys and organization IDs must not be used as fallbacks
- The provider should support an optional setting to expose the model's internal reasoning steps in the output; by default, reasoning content should be hidden
- When the model name is missing from the provider string, a clear error message should be shown explaining the correct format
- HTTP errors (client errors, rate limits, server errors) should be surfaced gracefully as error results rather than crashing, and relevant metadata such as HTTP status codes and rate-limit headers should be preserved
- The provider should support overriding the API endpoint via configuration or environment variables, following the same priority rules as other providers (explicit config wins over provider-level env, which wins over process env)

## Why This Matters

Without this integration, promptfoo users cannot easily include Abliteration models in their red-teaming or benchmarking pipelines. Adding proper first-class support makes these models available alongside every other provider in the ecosystem, with consistent authentication handling and configuration patterns.
