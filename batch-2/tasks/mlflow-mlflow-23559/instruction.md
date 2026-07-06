I'm working on extending the MLflow assistant's provider system in two ways, and I need some help getting everything wired up correctly.

*   The `list_providers()` function in `mlflow/assistant/providers/__init__.py` must return a list that includes a provider with `name='mlflow_gateway'`, `display_name='MLflow AI Gateway'`, and `is_available()` returning `True`.

*   The MLflow Gateway provider's `list_models()` method must raise `NotImplementedError` with a message containing 'Model listing is not supported'.

*   The MLflow Gateway provider's `check_connection()` method must raise `NotImplementedError` with a message containing 'verified by the frontend'.

*   The `list_providers()` function must return a list that includes a provider with `name='ollama'`, `display_name='Ollama'`, and `is_available()` returning `True`.

*   The Ollama provider's `list_models(base_url, api_key=None)` method must make a GET request to `{base_url}/api/tags` with `timeout=10` via the `requests` module imported at the package level in `mlflow/assistant/providers/__init__.py` (i.e., patchable at `mlflow.assistant.providers.requests.get`). When `api_key` is not provided, send `headers={}`. When `api_key` is provided, send `headers={'Authorization': 'Bearer {api_key}'}`. Returns a list of model name strings from the response's `models[*].model` field.

*   The Ollama provider's `list_models()` must raise `ProviderNotConfiguredError` (from `mlflow.assistant.providers.base`) when the HTTP GET fails with an exception. The error message must include the original exception message.

*   When `list_models()` is called on the Ollama provider with no `base_url` argument and no `base_url` is set in config, the request must default to `http://localhost:11434/api/tags`.

*   A new module `mlflow/assistant/providers/openai_compatible.py` must export: `OpenAICompatibleProvider` (class), `_strip_think_blocks` (function), `_merge_tool_call_chunk` (function), `_trim_session` (function), `_MAX_SESSION_BYTES` (int constant). This module must import `aiohttp` and `execute_tool` at the top level so they are patchable at `mlflow.assistant.providers.openai_compatible.aiohttp.ClientSession` and `mlflow.assistant.providers.openai_compatible.execute_tool`.

*   `_strip_think_blocks(buf, in_think)` must return a 3-tuple `(emit, remaining, new_in_think)`. For plain text: returns `(text, '', False)`. For a complete `<think>...</think>` block: strips it and returns surrounding text. When `<think>` starts but no closing tag yet: returns `('', '', True)`. When `in_think=True` and `</think>` appears: returns text after the closing tag. A partial opening tag at the end of `buf` (e.g., `'<th'`, `'<'`) must be held in `remaining` and NOT set `in_think=True`. A partial closing tag at the end when `in_think=True` (e.g., `'</th'`, `'<'`) must be held in `remaining` and stay `in_think=True`.

*   `_merge_tool_call_chunk(acc, chunk)` must mutate `acc` (a list) to accumulate streaming tool call deltas identified by `chunk['index']`. The first chunk for an index must copy `id` and `function.name`; all chunks must concatenate `function.arguments`. Multiple parallel tool calls at different indexes must be tracked independently.

*   `_trim_session(messages)` must drop the oldest non-system messages until the JSON-serialized total fits within `_MAX_SESSION_BYTES`. The system message at index 0 must always be preserved. The most recent messages must be retained.

*   `OpenAICompatibleProvider` must accept constructor keyword arguments: `name`, `display_name`, `description`, `connection_hint`, `list_models_fn` (optional, callable), `chat_url_builder` (optional, callable), `default_base_url` (optional str). When no `list_models_fn` is provided, `list_models()` raises `NotImplementedError` with a message containing 'Model listing is not supported'.

*   `OpenAICompatibleProvider.astream(prompt, tracking_uri)` must use `aiohttp.ClientSession` (imported in the module) to POST to the chat URL. The default URL is `{default_base_url}/v1/chat/completions`. If a `chat_url_builder` callable is given, it is called with `(base_url, tracking_uri)` to determine the URL. When `api_key` is in config, include `Authorization: Bearer {api_key}` in the request headers; otherwise use empty headers `{}`. The method must parse SSE lines (`data: {JSON}`), tolerate blank lines and comment lines (e.g., `:heartbeat`), and stop on `[DONE]`. Yield `EventType.STREAM_EVENT` events for content chunks, `EventType.DONE` at end, and `EventType.ERROR` with the response body text on HTTP non-200 responses.

*   `astream` must strip `<think>...</think>` blocks from the streamed content before emitting to users. Internal reasoning text inside these blocks must never appear in `EventType.STREAM_EVENT` payloads.

*   On tool calls, `astream` must accumulate chunked `tool_calls` deltas using `_merge_tool_call_chunk`, execute each tool via `execute_tool(tool_name, tool_args, ...)`, emit `EventType.MESSAGE` events for tool use, and include the tool result as a `role='tool'` message in the second request's messages list.

*   `ProviderConfig` in `mlflow/assistant/config.py` must include an `api_key: str | None = None` field. The `AssistantConfig.set_provider()` and `AssistantConfig.update_provider()` methods must accept an `api_key` keyword argument and persist it to the config. The saved value must survive a config load/save round-trip.

*   The `GET /ajax-api/3.0/mlflow/assistant/providers/{provider}/models` endpoint must read the API key from the `X-API-Key` request header and pass it as the second argument to `provider.list_models(base_url, api_key)`. If an `api_key` query parameter is passed instead, it must be ignored and `None` must be forwarded to the provider.


*   Interface details: ## New Module: `mlflow/assistant/providers/openai_compatible.py`

### Module-level imports required
This module must import `aiohttp` and `execute_tool` at the top level, because tests patch:
- `mlflow.assistant.providers.openai_compatible.aiohttp.ClientSession`
- `mlflow.assistant.providers.openai_compatible.execute_tool`

---

Type: Constant
Name: _MAX_SESSION_BYTES
Location: mlflow/assistant/providers/openai_compatible.py
Description: Integer constant for the maximum session byte size. Used by `_trim_session` to decide when to drop old messages.

---

Type: Function
Name: _strip_think_blocks
Location: mlflow/assistant/providers/openai_compatible.py
Signature: _strip_think_blocks(buf: str, in_think: bool) -> tuple[str, str, bool]
Description: Strips `<think>...</think>` spans from a streaming text buffer. Returns a 3-tuple: (emit_text, remaining_buf, new_in_think_flag). `emit_text` is the text safe to show users; `remaining_buf` holds any partial open/close tag that must be re-fed with the next chunk; `new_in_think_flag` indicates whether the parser is inside a think block after processing.

Behavior:
- Plain text with no `<think>` tags: returns `(buf, "", False)`
- Complete `<think>secret</think>` block: strips it, returns surrounding text only
- Opening `<think>` encountered: sets in_think=True, stops emitting until closing tag
- Partial opening tag at end of buf (e.g., `"foo<th"`): emits text before the partial tag, holds the partial in remaining_buf; does NOT set in_think=True
- Partial closing tag at end when in_think=True (e.g., `"secret</th"`): holds partial in remaining_buf, stays in_think=True
- When in_think=True and no closing tag: returns `("", partial_or_empty, True)`

---

Type: Function
Name: _merge_tool_call_chunk
Location: mlflow/assistant/providers/openai_compatible.py
Signature: _merge_tool_call_chunk(acc: list, chunk: dict) -> None
Description: Mutates `acc` (a list of accumulated tool call dicts) by merging a streaming tool call delta `chunk`. Uses `chunk["index"]` to identify target entry; copies `chunk["id"]` and `chunk["function"]["name"]` from first chunk for that index; appends `chunk["function"]["arguments"]` to accumulate the full argument string. Supports multiple parallel tool calls at different indexes.

---

Type: Function
Name: _trim_session
Location: mlflow/assistant/providers/openai_compatible.py
Signature: _trim_session(messages: list) -> list
Description: Trims the conversation history to fit within `_MAX_SESSION_BYTES` when JSON-encoded. Always preserves the system message at index 0. Drops the oldest non-system messages first, keeping the most recent messages intact.

---

Type: Class
Name: OpenAICompatibleProvider
Location: mlflow/assistant/providers/openai_compatible.py
Description: Provider for any server exposing a POST /v1/chat/completions endpoint in OpenAI SSE form. Instantiated with preset-specific data; the same class can be registered multiple times for different presets.

Constructor Signature:
```
OpenAICompatibleProvider(
    name: str,
    display_name: str,
    description: str,
    connection_hint: str,
    list_models_fn=None,      # optional callable(base_url, api_key) -> list[str]
    chat_url_builder=None,    # optional callable(base_url, tracking_uri) -> str | None
    default_base_url: str | None = None,
    # additional optional keyword args allowed
)
```

Methods:

`name` (property) -> str: Returns the provider's identifier string.
`display_name` (property) -> str: Returns the human-readable provider name.
`is_available() -> bool`: Always returns True.

`list_models(base_url: str | None = None, api_key: str | None = None) -> list[str]`:
  - If no `list_models_fn` was provided at construction, raises `NotImplementedError` with a message containing "Model listing is not supported".
  - Otherwise delegates to the provided function.

`check_connection(echo=None) -> None`:
  - If no `list_models_fn` was provided (e.g., the MLflow Gateway preset), raises `NotImplementedError` with a message containing "verified by the frontend".

`astream(prompt: str, tracking_uri: str, ...) -> AsyncGenerator[Event, None]`:
  - Async generator yielding Event objects.
  - Builds chat URL: uses `chat_url_builder(base_url, tracking_uri)` if provided; otherwise appends `/v1/chat/completions` to the configured/default base URL.
  - Reads `api_key` from provider config; sends `{"Authorization": "Bearer {api_key}"}` header when set; sends `{}` when not set.
  - Sends POST request via `aiohttp.ClientSession` (imported in this module) in SSE streaming mode.
  - Parses SSE lines (`data: {JSON}\n`); skips blank lines, comment lines (starting with `:`), and the `[DONE]` terminator.
  - Emits `EventType.STREAM_EVENT` with `data={"event": {"delta": {"text": "..."}}}` for each content chunk.
  - Strips `<think>...</think>` blocks from streamed content before emitting.
  - Emits `EventType.DONE` when stream ends cleanly.
  - On HTTP non-200 status: yields `EventType.ERROR` event with `data={"error": "<response body text>"}`.
  - On tool calls: accumulates chunked `tool_calls` delta via `_merge_tool_call_chunk`; calls `execute_tool(tool_name, tool_args, ...)` (imported in this module); emits `EventType.MESSAGE` for tool use; sends second request with the tool result appended as `role: "tool"` message.

---

## Modified Module: `mlflow/assistant/providers/__init__.py`

### Module-level import required
The module must import `requests` at the top level because tests patch `mlflow.assistant.providers.requests.get` to intercept Ollama's model listing HTTP call.

Type: Function
Name: list_providers
Location: mlflow/assistant/providers/__init__.py
Signature: list_providers() -> list[AssistantProvider]
Description: Returns a list of all registered provider instances. Must include:
- A provider with `name="mlflow_gateway"`, `display_name="MLflow AI Gateway"`, `is_available() is True`; `list_models()` raises `NotImplementedError` matching "Model listing is not supported"; `check_connection()` raises `NotImplementedError` matching "verified by the frontend"
- A provider with `name="ollama"`, `display_name="Ollama"`, `is_available() is True`; `list_models(base_url, api_key)` makes a GET to `{base_url}/api/tags` via the `requests` module imported in this package (patch path: `mlflow.assistant.providers.requests.get`); default base URL is `http://localhost:11434`

---

## Modified File: `mlflow/assistant/config.py`

Type: Class
Name: ProviderConfig
Location: mlflow/assistant/config.py
Description: Pydantic model for per-provider config. Must include field `api_key: str | None = None`.

Type: Class
Name: AssistantConfig
Location: mlflow/assistant/config.py
Description: Top-level assistant config Pydantic model.

Signature: `set_provider(provider_name: str, model: str, permissions=None, base_url: str | None = None, api_key: str | None = None) -> None`
Signature: `update_provider(provider_name: str, model: str | None = None, permissions=None, base_url: str | None = None, api_key: str | None = None) -> None`

---

## Modified File: `mlflow/assistant/providers/base.py`

Type: Class
Name: AssistantProvider (abstract base)
Location: mlflow/assistant/providers/base.py
Description: Base class for all providers. The `list_models` method signature must be updated.

Signature: `list_models(base_url: str | None = None, api_key: str | None = None) -> list[str]`

---

## Modified File: `mlflow/server/assistant/api.py`

The `list_provider_models` endpoint handler must:
- Accept the `X-API-Key` request header (FastAPI `Header` parameter with alias `"X-API-Key"`)
- Call `provider.list_models(base_url, api_key_from_header)` where `api_key_from_header` is the value from `X-API-Key` header (or `None` if absent)
- NOT forward any `api_key` query parameter to the provider


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.