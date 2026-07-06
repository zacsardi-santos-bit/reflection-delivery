Implement the `ProviderToolSearchMiddleware` class to manage tool deferral in model requests, leveraging server-side tool search features from AI providers. Ensure the middleware automatically marks tools as "deferred" and injects the appropriate provider-specific search descriptor.

*   Implement the `ProviderToolSearchMiddleware` class in `libs/langchain_v1/langchain/agents/middleware/provider_tool_search.py`.
    *   Constructor signature: `__init__(self, *, searchable_tools: list[str | BaseTool] | None = None) -> None`.
    *   Default `searchable_tools` to `None` if not provided.
*   Ensure `ProviderToolSearchMiddleware` is importable from `langchain.agents.middleware` and added to its `__all__` list.
*   Implement `wrap_model_call` and `awrap_model_call` methods:
    *   `wrap_model_call(self, request: ModelRequest, handler: Callable[[ModelRequest], ModelResponse]) -> ModelResponse`.
    *   `awrap_model_call(self, request: ModelRequest, handler: Callable[[ModelRequest], Awaitable[ModelResponse]]) -> Awaitable[ModelResponse]`.
    *   Both methods must apply the same deferral and validation logic, with `awrap_model_call` awaiting the handler.
*   Ensure tools in `searchable_tools` have their `extras` dict updated with `defer_loading: True`, preserving existing keys.
*   Trigger deferral for tools with `extras['defer_loading'] = True` at construction time, regardless of `searchable_tools`.
*   Pass dictionary-form tools unchanged without errors.
*   Append provider-specific search tool descriptors when tools are deferred:
    *   Anthropic: `{"type": "tool_search_tool_bm25_20251119", "name": "tool_search_tool_bm25"}`.
    *   OpenAI: `{"type": "tool_search"}`.
*   Raise a `ValueError` if `searchable_tools` references unbound tool names, listing them in sorted order with the phrase 'not bound to the model'.
*   Ensure unbound-tool validation runs before provider-detection.
*   Raise a `ValueError` with 'server-side tool search' if a deferred tool's provider is unsupported.
*   Raise a `ValueError` with 'could not determine the provider' if the provider cannot be determined.
*   Pass requests through unchanged if no tools are deferred.
*   Implement provider detection using a fallback strategy:
    *   Call `_model_params(config)` if available, checking `model_provider` and `model`.
    *   Check `_default_config` for `model_provider` or `model`.
    *   Call `_get_ls_params()` and read `ls_provider`.
    *   Match model class names for Anthropic and OpenAI.
*   Normalize provider identifiers by lowercasing and replacing hyphens with underscores.
*   Use the part before a colon in `model` as the provider, applying heuristics for specific prefixes.
*   Merge default config with runtime params, prioritizing `model_provider` over `model`.
*   Treat non-mapping `runtime.config` as `None`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.