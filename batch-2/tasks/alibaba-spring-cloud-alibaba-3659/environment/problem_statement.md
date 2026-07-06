## Description

We are introducing a new AI integration module to Spring Cloud Alibaba that allows Spring Cloud microservice applications to connect with a large-language-model chat service. As part of this integration, a configuration options class is needed that encapsulates all tunable parameters for the chat API.

The options class should support a builder pattern so that developers can construct a configuration object with optional overrides. When a developer uses the builder without specifying any values, the resulting configuration object must be valid and non-null, with sensible defaults already in place. In particular, the maximum token limit for responses should default to 1500 when no explicit value is provided.

## Expected Behavior

- The options class must support construction via a builder with no required arguments.
- A freshly built options object (with no builder configuration) must be a non-null, usable configuration.
- The default maximum token limit must be 1500 when no value is explicitly set.

## Why This Matters

Developers who want to quickly integrate the AI chat service should not need to configure every option manually. Sensible defaults — especially around response size limits — mean the integration works out of the box. Without a proper default for the token limit, the client may behave unpredictably or fail to initialize correctly on first use.
