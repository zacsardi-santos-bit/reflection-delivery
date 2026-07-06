Implement a new experimental module in the official Anthropic partner package for LangChain that supports tool-calling and structured data extraction with Claude models. Ensure the module provides a chat model class with all standard operations and the ability to bind schemas as tools for structured output.

*   Create the module `libs/partners/anthropic/langchain_anthropic/experimental.py` and export the class `ChatAnthropicTools`.
*   Implement `ChatAnthropicTools` to support synchronous and asynchronous operations: `stream()`, `astream()`, `batch()`, `abatch()`, `invoke()`, and `ainvoke()`, returning messages with string content.
*   Allow `ChatAnthropicTools` to accept a `model_name` parameter and optional configuration parameters via `config` during instantiation.
*   Ensure `ChatAnthropicTools` functions correctly in prompt chains with system messages, returning responses with string content.
*   Implement a `bind_tools(tools)` method in `ChatAnthropicTools` to:
    *   Convert tool schemas to OpenAI function format.
    *   Bind tools to the model using a 'tools' keyword argument.
    *   Inject tool descriptions into the system prompt as XML within a `<tools>` tag.
*   Implement a `with_structured_output(schema)` method to:
    *   Accept a Pydantic `BaseModel` class.
    *   Return a `Runnable` chain that, when invoked with text, returns a correctly instantiated Pydantic model with typed fields.
*   Parse XML-formatted tool call responses into structured tool call objects:
    *   Detect tool calls and set response message content to an empty string.
    *   Place parsed tool calls in `additional_kwargs['tool_calls']` as a list of dicts with 'type' and 'function' keys.
*   Require the `defusedxml` library for XML parsing:
    *   Raise `ImportError` if not installed, with a message and installation instructions.
*   Update `ChatAnthropic` class in `chat_models.py` to expose `_format_output(data, **kwargs)`:
    *   Ensure it is called from `_generate` and `_agenerate` with all kwargs forwarded for subclass customization.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.