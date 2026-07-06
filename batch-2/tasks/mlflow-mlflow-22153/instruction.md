I'm working on consolidating duplicated gateway provider logic in the codebase.

*   The _get_provider_instance function in mlflow/metrics/genai/model_utils.py must handle the 'gateway' provider: call get_gateway_config(model) (available at mlflow.metrics.genai.model_utils.get_gateway_config) with the model/endpoint name, and return a _MlflowGatewayProvider instance.

*   The _MlflowGatewayProvider class must be defined in mlflow/metrics/genai/model_utils.py and accept an EndpointConfig as its first constructor argument and an optional extra_headers dict as second argument.

*   The _MlflowGatewayProvider.get_endpoint_url(endpoint_type) method must return a URL that ends with '/chat/completions' (derived from the api_base in the underlying config).

*   The _MlflowGatewayProvider.headers property must return the extra_headers dict passed at construction, or an empty dict if none was provided.

*   The _MlflowGatewayProvider.config attribute must expose a model.name field equal to the endpoint/model name used to create the provider.

*   The _MlflowGatewayProvider must have an adapter_class attribute compatible with OpenAI-style chat format conversion (chat_to_model and model_to_chat methods).

*   The _get_provider_instance function must raise MlflowException with a message matching 'not supported' when given an unrecognized provider name.

*   _invoke_via_gateway in mlflow/genai/judges/adapters/gateway_adapter.py must handle string prompts by delegating to score_model_on_payload (looked up at mlflow.genai.judges.adapters.gateway_adapter.score_model_on_payload) with the string as the payload keyword argument.

*   _invoke_via_gateway must handle list (message) prompts by using _get_provider_instance and _send_request from mlflow.metrics.genai.model_utils, forwarding messages and inference_params in the payload.

*   The gateway_adapter module must import and use _get_provider_instance from mlflow.metrics.genai.model_utils (not a local _get_provider function) for provider resolution in the invoke-and-handle-tools loop.

*   When _call_llm in mlflow/genai/discovery/utils.py receives a 'gateway:/' URI and LiteLLM is not available, it must call _get_provider_instance('gateway', endpoint_name) and _send_request from mlflow.metrics.genai.model_utils, and return a response with choices[0].message.content set to the gateway's reply.

*   The _get_provider_instance function for 'gateway' must use the GatewayConfig's extra_headers as the provider's headers (e.g., {'X-Custom': 'header'} must appear verbatim in provider.headers).


*   Interface details: Type: Class
Name: _MlflowGatewayProvider
Location: mlflow/metrics/genai/model_utils.py
Description: OpenAI-compatible provider class for MLflow AI Gateway endpoints. Extends the OpenAI provider with gateway-specific header handling.
Signature: __init__(config: EndpointConfig, extra_headers: dict[str, str] | None = None) -> None
Methods:
  - get_endpoint_url(endpoint_type: str) -> str  # returns URL ending in /chat/completions
  - headers (property) -> dict[str, str]  # returns extra_headers or empty dict
  - config: object with config.model.name (str) attribute
  - adapter_class: class attribute for chat format conversion

Type: Function
Name: _get_provider_instance
Location: mlflow/metrics/genai/model_utils.py
Signature: _get_provider_instance(provider: str, model: str) -> BaseProvider
Description: Returns a configured provider instance for the given provider name and model name. Supports "openai", "anthropic", "gemini", "mistral", and "gateway" providers. For "gateway", calls get_gateway_config(model) (looked up at mlflow.metrics.genai.model_utils.get_gateway_config) and returns a _MlflowGatewayProvider. Raises MlflowException with a message matching "not supported" for unsupported providers.

Type: Function
Name: _invoke_via_gateway
Location: mlflow/genai/judges/adapters/gateway_adapter.py
Signature: _invoke_via_gateway(model_uri: str, provider: str, prompt: str | list, inference_params: dict | None = None, ...) -> str
Description: Invokes a model via the gateway. For string prompts, delegates to score_model_on_payload(model_uri, payload=prompt, ...) (looked up at mlflow.genai.judges.adapters.gateway_adapter.score_model_on_payload). For list (message) prompts, uses _get_provider_instance and _send_request from mlflow.metrics.genai.model_utils.

Note: _get_provider and _get_mlflow_gateway_provider are removed from mlflow/genai/judges/adapters/gateway_adapter.py. Tests that previously patched mlflow.genai.judges.adapters.gateway_adapter._get_provider must now patch mlflow.genai.judges.adapters.gateway_adapter._get_provider_instance.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.