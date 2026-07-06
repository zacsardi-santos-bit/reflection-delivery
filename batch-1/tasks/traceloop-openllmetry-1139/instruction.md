Implement an OpenTelemetry instrumentation package for the Mistral AI client library to automatically capture trace spans for all Mistral AI requests. Ensure that both synchronous and asynchronous client calls are supported, capturing detailed information about each request and response.

*   Create a class `MistralAiInstrumentor` in `packages/opentelemetry-instrumentation-mistralai/opentelemetry/instrumentation/mistralai/__init__.py`.
    *   Extend `BaseInstrumentor`.
    *   Implement the `instrument()` method to patch both the synchronous and asynchronous Mistral AI client methods (`chat`, `chat_stream`, `embeddings`).

*   For chat completion requests (both synchronous and asynchronous):
    *   Produce a finished span named 'mistralai.chat'.
    *   Include the following attributes in the span:
        *   `gen_ai.system` set to 'MistralAI'.
        *   `llm.request.type` set to 'chat'.
        *   `gen_ai.request.model` set to the model name used.
        *   `gen_ai.prompt.0.content` set to the content of the first message in the request.
    *   For non-streaming requests:
        *   Set `llm.is_streaming` to falsy.
        *   Set `gen_ai.completion.0.content` to the content of the first choice's message in the response.
    *   For streaming requests:
        *   Set `llm.is_streaming` to truthy.
        *   Set `gen_ai.completion.0.content` to the full assembled text from all streamed content deltas.
    *   Record token usage:
        *   `gen_ai.usage.prompt_tokens` for prompt tokens used.
        *   `gen_ai.usage.completion_tokens` for completion tokens used.
        *   `llm.usage.total_tokens` as the sum of `gen_ai.usage.prompt_tokens` and `gen_ai.usage.completion_tokens`.

*   For embeddings requests (both synchronous and asynchronous):
    *   Produce a finished span named 'mistralai.embeddings'.
    *   Include the following attributes in the span:
        *   `gen_ai.system` set to 'MistralAI'.
        *   `llm.request.type` set to 'embedding'.
        *   `llm.is_streaming` set to falsy.
        *   `gen_ai.request.model` set to the model name.
        *   `gen_ai.prompt.0.content` set to the input text, or the first element if the input is a list.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.