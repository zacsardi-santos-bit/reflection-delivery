I'm working on the MLflow judge evaluation system and running into a frustrating limitation.

*   GatewayAdapter.is_applicable must return True for model URIs with providers: openai, anthropic, gemini, mistral, gateway. It must return True for endpoints:/ URIs only when the prompt is a plain string. It must return False for endpoints:/ URIs when the prompt is a list of message objects. It must return False for unrecognized providers.

*   GatewayAdapter.invoke must, when a trace is supplied, dispatch through _invoke_and_handle_tools with the parsed provider string, model name, and trace object; the returned result's feedback attribute must have name, value, rationale, and trace_id fields populated correctly from the LLM response JSON.

*   GatewayAdapter.invoke must, when no trace is supplied, dispatch through _invoke_via_gateway and return a result whose feedback.value is parsed from the JSON response.

*   GatewayAdapter.invoke must raise MlflowException with a message matching 'base_url and extra_headers are not supported' when a base_url is provided for an endpoints:/ URI.

*   GatewayAdapter.invoke must raise MlflowException with a message matching 'Trace-based tool calling is not supported' when a trace is provided for an endpoints:/ URI.

*   GatewayAdapter.invoke_with_structured_output must return a validated Pydantic model instance when the response JSON is valid. It must raise MlflowException with a message matching 'Failed to parse response' when the response is not valid JSON.

*   GatewayAdapter._invoke_and_handle_tools must return an InvokeOutput with response, num_prompt_tokens, and num_completion_tokens populated from the LLM response for a single-shot call with no tool calls.

*   GatewayAdapter._invoke_and_handle_tools must execute a multi-turn tool-calling loop: on a tool-call response it processes the tool calls and sends a follow-up request, returning InvokeOutput once a non-tool-call response is received.

*   GatewayAdapter._invoke_and_handle_tools must, upon receiving a context window error during the tool loop, call _remove_oldest_tool_call_pair to prune messages and retry the request.

*   GatewayAdapter._invoke_and_handle_tools must raise MlflowException with a message matching 'iteration limit of N exceeded' (where N is the value of the MLFLOW_JUDGE_MAX_ITERATIONS environment variable) when the tool-calling loop exceeds the maximum allowed iterations.

*   GatewayAdapter._invoke_and_handle_tools must, when the first request fails due to an unsupported response_format, retry the request without the response_format parameter.

*   GatewayAdapter._invoke_and_handle_tools must use the base_url as the endpoint URL when a base_url argument is provided.

*   _invoke_via_gateway must wrap a plain string prompt as [{'role': 'user', 'content': prompt}] in the messages payload. When prompt is already a list, pass it through unchanged. The payload model field must be set to the endpoint name (not a litellm-format string). The MLFLOW_GATEWAY_CALLER_HEADER header must be set to GatewayCaller.JUDGE.value. The endpoint URL must be api_base + 'chat/completions'. Any inference_params dict is merged into the payload.

*   _build_request must return a dict containing the serialized messages. When tools are provided, it must also include the tools list and tool_choice set to 'auto'. When inference_params is provided, those key-value pairs must be merged into the returned dict.

*   _parse_response_message must return a (ChatMessage, usage) tuple from an OpenAI-format response. It must raise MlflowException matching 'Empty choices' when the choices list is empty. For Anthropic-format responses, text content must be placed in ChatMessage.content and tool_use blocks must be converted into ChatMessage.tool_calls entries.

*   _remove_oldest_tool_call_pair must remove the oldest assistant message containing tool_calls and the corresponding tool response message, returning the remaining messages. It must return None when no tool calls are present. It must handle both ChatMessage objects and dict-style tool_calls representations.

*   is_context_window_error must return True for messages like 'maximum context length exceeded', 'This model's maximum context length is 8192 tokens', and 'Request too large: too many tokens'. It must return False for unrelated error messages.

*   _get_provider must return a provider object whose config.model.name equals the given model_name and whose get_endpoint_url result points to the provider's API. For openai, the URL must contain 'openai.com' and the headers must contain an authorization or api-key entry. For anthropic, the URL must contain 'anthropic.com'. It must raise MlflowException matching 'not supported' for unrecognized providers such as 'cohere'.

*   _get_mlflow_gateway_provider must return a provider object where get_endpoint_url returns api_base + 'chat/completions', config.model.name equals the endpoint_name, and the adapter_class converts the payload with model set to endpoint_name.

*   _get_max_context_tokens must look up 'provider/model_name' key first in the model cost data, returning its max_input_tokens value. If not found, it falls back to the 'model_name' key. It returns None when the model is not in the data. The 'provider/model_name' key takes priority when both keys exist.

*   _should_proactively_prune must return True when prompt_tokens from the usage dict is strictly greater than threshold * max_context_tokens (default threshold=0.85). It must return False when prompt_tokens equals exactly threshold * max_context_tokens. It must return False when max_context_tokens is None or when 'prompt_tokens' is absent from usage.

*   When proactive pruning is active and prompt token usage exceeds the threshold during a tool-calling loop, _remove_oldest_tool_call_pair must be called to prune the conversation before the next request.

*   When litellm is unavailable, the adapter selection system must choose GatewayAdapter for model URIs with providers openai, anthropic, gemini, mistral, gateway, and for endpoints:/ URIs with a string prompt. It must return no supported adapter for endpoints:/ URIs with a list prompt and for unknown providers.

*   When litellm is unavailable, calling invoke_judge_model with a trace must succeed (not raise an error), returning feedback with the parsed value from the LLM response.

*   The score_model_on_payload function used by GatewayAdapter must be imported from mlflow.genai.judges.adapters.gateway_adapter (i.e., it must live in or be re-exported from that module), not from mlflow.metrics.genai.model_utils.

*   The _NATIVE_PROVIDERS symbol must no longer be exported from mlflow.genai.judges.utils. Providers openai, anthropic, gemini, mistral, endpoints, gateway, cohere, and groq must all work with make_judge when litellm is unavailable.


*   Interface details: Type: Class
Name: GatewayAdapter
Location: mlflow/genai/judges/adapters/gateway_adapter.py
Description: Adapter that communicates with AI providers (OpenAI, Anthropic, Gemini, Mistral, gateway endpoints) via the MLflow gateway infrastructure, without requiring LiteLLM.
Signature:
  is_applicable(model_uri: str, prompt) -> bool  # classmethod
  invoke(input_params: AdapterInvocationInput) -> result  # result has .feedback attribute
  invoke_with_structured_output(model_uri: str, messages: list, output_schema: type) -> pydantic.BaseModel
  _invoke_and_handle_tools(provider: str, model_name: str, messages: list, trace, num_retries: int, response_format=None, base_url=None) -> InvokeOutput

Type: Class
Name: InvokeOutput
Location: mlflow/genai/judges/adapters/gateway_adapter.py
Description: Data container returned by _invoke_and_handle_tools holding the response text and token usage.
Fields: response (str), request_id (str or None), num_prompt_tokens (int or None), num_completion_tokens (int or None)

Type: Function
Name: _build_request
Location: mlflow/genai/judges/adapters/gateway_adapter.py
Signature: _build_request(messages: list, tools, response_format, include_response_format: bool, inference_params: dict or None) -> dict
Description: Builds a chat request payload dict. Includes messages; adds tools + tool_choice="auto" when tools are provided; merges inference_params into the payload.

Type: Function
Name: _get_max_context_tokens
Location: mlflow/genai/judges/adapters/gateway_adapter.py
Signature: _get_max_context_tokens(provider: str, model_name: str) -> int or None
Description: Returns the max input token count for a model. Looks up "provider/model_name" first; falls back to "model_name" alone; returns None if not found. The underlying model cost data is accessed via a module-level function (_get_model_cost) that must be patchable at mlflow.genai.judges.adapters.gateway_adapter._get_model_cost.

Type: Function
Name: _get_mlflow_gateway_provider
Location: mlflow/genai/judges/adapters/gateway_adapter.py
Signature: _get_mlflow_gateway_provider(endpoint_name: str) -> provider object
Description: Returns a provider object configured to use the MLflow gateway. The endpoint URL is api_base + "chat/completions"; config.model.name equals endpoint_name. Uses get_gateway_config internally (which must be patchable at mlflow.genai.judges.adapters.gateway_adapter.get_gateway_config).

Type: Function
Name: _get_provider
Location: mlflow/genai/judges/adapters/gateway_adapter.py
Signature: _get_provider(provider: str, model_name: str) -> provider object
Description: Returns a configured provider object for the given provider name and model. Raises MlflowException with a message matching "not supported" for unrecognized providers. Must be patchable at mlflow.genai.judges.adapters.gateway_adapter._get_provider.

Type: Function
Name: _parse_response_message
Location: mlflow/genai/judges/adapters/gateway_adapter.py
Signature: _parse_response_message(response: dict, provider) -> tuple[ChatMessage, usage_dict]
Description: Parses an LLM response dict into a (ChatMessage, usage) tuple. Supports both OpenAI-compatible and Anthropic response formats. Raises MlflowException matching "Empty choices" when the choices list is empty.

Type: Function
Name: _should_proactively_prune
Location: mlflow/genai/judges/adapters/gateway_adapter.py
Signature: _should_proactively_prune(usage: dict, max_context_tokens: int or None, threshold: float = 0.85) -> bool
Description: Returns True when prompt_tokens from usage is strictly greater than threshold * max_context_tokens. Returns False when max_context_tokens is None or when prompt_tokens is absent from usage.

Type: Function
Name: _invoke_via_gateway
Location: mlflow/genai/judges/adapters/gateway_adapter.py
Signature: _invoke_via_gateway(model_uri: str, provider: str, prompt, inference_params=None) -> str
Description: Invokes the gateway endpoint directly. String prompts are wrapped into [{"role": "user", "content": prompt}]. The payload model field is set to the endpoint name. The MLFLOW_GATEWAY_CALLER_HEADER header is set to GatewayCaller.JUDGE.value. The endpoint URL is constructed as api_base + "chat/completions". inference_params are merged into the payload. Uses send_chat_request internally (patchable at mlflow.genai.judges.adapters.gateway_adapter.send_chat_request).

Type: Class
Name: ChatCompletionError
Location: mlflow/genai/judges/adapters/utils.py
Description: Exception class representing a chat completion failure.
Fields: status_code (int), message (str), is_context_window_error (bool, default False)

Type: Function
Name: is_context_window_error
Location: mlflow/genai/judges/adapters/utils.py
Signature: is_context_window_error(message: str) -> bool
Description: Returns True when the message indicates a context window or token limit was exceeded (e.g., "maximum context length exceeded", "too many tokens", "context length"). Returns False for unrelated errors.

Type: Function
Name: _remove_oldest_tool_call_pair
Location: mlflow/genai/judges/utils/tool_calling_utils.py
Signature: _remove_oldest_tool_call_pair(messages: list) -> list or None
Description: Removes the oldest assistant message with tool_calls and its corresponding tool response message(s). Returns the pruned message list, or None if no tool calls exist in the conversation. Works with message objects that have role, tool_calls, and tool_call_id attributes, as well as dict-style tool_calls entries.

Type: Class
Name: GatewayConfig
Location: mlflow/genai/utils/gateway_utils.py
Description: Dataclass holding direct-HTTP gateway endpoint configuration (no LiteLLM-specific fields).
Fields: api_base (str), endpoint_name (str), extra_headers (dict[str, str] or None)

Type: Function
Name: get_gateway_config
Location: mlflow/genai/utils/gateway_utils.py
Signature: get_gateway_config(endpoint_name: str) -> GatewayConfig
Description: Returns a GatewayConfig for the given MLflow gateway endpoint name. The api_base is constructed from the resolved gateway URI. This function must also be importable as a module-level name from mlflow.genai.judges.adapters.gateway_adapter (patchable at mlflow.genai.judges.adapters.gateway_adapter.get_gateway_config).

Note on module-level imports in gateway_adapter.py: The following names must be available as module-level attributes of mlflow.genai.judges.adapters.gateway_adapter (imported at the top of the file, not inside functions) so that test mocking at those paths works:
- score_model_on_payload (from mlflow.metrics.genai.model_utils)
- send_chat_request (from mlflow.genai.judges.adapters.utils)
- get_gateway_config (from mlflow.genai.utils.gateway_utils)
- _get_model_cost (from mlflow.utils.providers or equivalent)
- _get_provider (defined locally)
- _process_tool_calls (from mlflow.genai.judges.utils.tool_calling_utils)
- _remove_oldest_tool_call_pair (from mlflow.genai.judges.utils.tool_calling_utils)
- _invoke_via_gateway (defined locally)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.