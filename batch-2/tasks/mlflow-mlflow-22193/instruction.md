I'm working on improving LLM token cost tracking in a system that routes requests through gateway endpoints.

*   The _TokenCounter class constructor must accept a required `model` parameter as its first argument (before optional parameters like input_tokens, output_tokens, cost_usd). The constructor must store the resolved model string in a `_model` instance attribute.

*   When _TokenCounter is initialized with a model URI whose provider is 'gateway' (e.g., 'gateway:/my-endpoint'), it must call _resolve_model_for_gateway with the endpoint name portion and store the resolved model string in `_model`. If the model URI is not a gateway URI, _resolve_model_for_gateway must NOT be called and the model string is stored in `_model` as-is.

*   The _resolve_model_for_gateway function must accept an endpoint_name string and return a 'provider:/model_name' formatted string if the gateway store returns a valid endpoint with at least one model mapping that has a model_definition. It must return None if: the endpoint has no model mappings, the first model mapping has no model_definition, or any exception is raised during the lookup.

*   The _fetch_model_cost function signature must accept two separate string parameters: provider and model_name. It must be decorated with lru_cache (providing a cache_clear() method). It must look up the (provider, model_name) tuple as the key in the cost map returned by mlflow.utils.providers._get_model_cost(). It returns a _ModelCost instance if found, or None if the key is not in the map.

*   The _lookup_model_cost function must parse both the provider and model_name from the model URI string and pass them as separate arguments to _fetch_model_cost(provider, model_name). The returned cost is computed as input_tokens * input_cost_per_token + output_tokens * output_cost_per_token.

*   _call_llm_via_gateway must be importable from mlflow.genai.utils.llm_utils. It must accept a model URI string, a messages list, and an optional token_counter keyword argument. The token_counter's _model attribute must remain unchanged (preserving the pre-resolved gateway model) after the call completes.

*   The _get_provider_instance and _send_request names must be available at the mlflow.genai.utils.llm_utils module level (i.e., patchable at 'mlflow.genai.utils.llm_utils._get_provider_instance' and 'mlflow.genai.utils.llm_utils._send_request').

*   The _get_gateway_endpoint handler in mlflow/server/handlers.py must accept a GetGatewayEndpoint request message with optional endpoint_id and optional name fields. When endpoint_id is provided, it must call the store's get_gateway_endpoint with endpoint_id=<value> and name=None. When name is provided, it must call get_gateway_endpoint with endpoint_id=None and name=<value>. The handler must return an HTTP 200 response on success.

*   GatewayEndpoint and GetGatewayEndpoint must be importable from mlflow.protos.service_pb2. _get_gateway_endpoint must be importable from mlflow.server.handlers.


*   Interface details: Type: Function
Name: _resolve_model_for_gateway
Location: mlflow/genai/utils/llm_utils.py
Signature: _resolve_model_for_gateway(endpoint_name: str) -> str | None
Description: Resolves a gateway endpoint name to its underlying provider model URI in the format "provider:/model_name". Returns None if the endpoint has no model mappings, no model definition, or if any exception occurs during lookup.

Type: Class
Name: _TokenCounter
Location: mlflow/genai/utils/llm_utils.py
Description: Thread-safe accumulator for LLM token usage and cost tracking. The constructor signature requires model as its first argument, before optional parameters. If the model URI provider is "gateway", it automatically calls _resolve_model_for_gateway and stores the resolved model in the _model attribute; otherwise stores the model string directly in _model.
Signature: __init__(self, model: str, input_tokens: int = 0, output_tokens: int = 0, cost_usd: float = 0.0)

Type: Function
Name: _fetch_model_cost
Location: mlflow/genai/utils/llm_utils.py
Signature: _fetch_model_cost(provider: str, model_name: str) -> _ModelCost | None
Description: LRU-cached function that looks up model cost information. Uses (provider, model_name) tuple as the key in the cost map returned by mlflow.utils.providers._get_model_cost(). Returns a _ModelCost instance if found, None otherwise. Exposes cache_clear() method.

Type: Function
Name: _lookup_model_cost
Location: mlflow/genai/utils/llm_utils.py
Signature: _lookup_model_cost(model_uri: str, input_tokens: int, output_tokens: int) -> float | None
Description: Parses both provider and model_name from model_uri, calls _fetch_model_cost(provider, model_name), and returns the total cost as input_tokens * input_cost_per_token + output_tokens * output_cost_per_token. Returns None if model not found.

Type: Function
Name: _call_llm_via_gateway
Location: mlflow/genai/utils/llm_utils.py
Signature: _call_llm_via_gateway(model: str, messages: list, ..., token_counter=None) -> ChatCompletionResponse
Description: Calls an LLM via a gateway endpoint. Must be importable from this module. _get_provider_instance and _send_request must be available as names at the mlflow.genai.utils.llm_utils module level (patchable via mock.patch at "mlflow.genai.utils.llm_utils._get_provider_instance" and "mlflow.genai.utils.llm_utils._send_request").

Type: Function
Name: _get_gateway_endpoint
Location: mlflow/server/handlers.py
Signature: _get_gateway_endpoint() -> Response
Description: HTTP handler that retrieves a gateway endpoint by either endpoint_id or name. Reads a GetGatewayEndpoint proto message. Calls the tracking store's get_gateway_endpoint(endpoint_id=<value_or_None>, name=<value_or_None>) with the provided field value and None for the unused field. Returns HTTP 200 on success.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.