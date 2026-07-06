## Description

We need a new LangChain partner integration package for the Groq inference service. Groq offers fast language model inference, and many LangChain users want to use it seamlessly within their existing LangChain workflows. Currently, there is no officially supported way to connect to Groq's API using LangChain's standard chat model interface.

## Expected Behavior

- A new installable package should expose a chat model class that works like any other LangChain chat model, supporting synchronous invocation, asynchronous invocation, and streaming.
- The model should accept an API key either via constructor argument or an environment variable.
- The API key must be treated as a secret: it should never appear in string representations of the model object and must not be included in serialized output.
- The model must support full serialization and deserialization — saving a configured model and restoring it (potentially with a different API key from a secrets store) should preserve all non-secret configuration fields.
- The model name should be configurable using either of two supported keyword argument names — a concise short form and a more descriptive long form — both controlling the same underlying model setting.
- Unknown constructor keyword arguments should be accepted as extra model parameters (with a warning), but if the same key is provided twice, or if a core parameter is passed as an extra, a clear error should be raised.
- Streaming mode with multiple completions per request should raise a clear error at construction time rather than failing silently at inference time.

## Why This Matters

Developers using Groq for fast inference should be able to plug it into any existing LangChain chain, agent, or workflow without special-casing. Having a proper partner package ensures consistent behavior around security, serialization, and configuration — the same guarantees users expect from other LangChain chat model integrations.
