I'm working on MLflow's evaluation and discovery pipeline, which makes LLM calls internally.

*   The GatewayLiteLLMConfig class must be defined in mlflow/genai/utils/gateway_utils.py and accept the following constructor arguments: model (str), api_base (str), api_key (str), extra_headers (dict), with those exact attribute names accessible on the instance.

*   The get_gateway_litellm_config function must be defined in mlflow/genai/utils/gateway_utils.py, accept a single string argument (the gateway endpoint name), and return a GatewayLiteLLMConfig instance.

*   When _call_llm is called with a model URI that starts with 'gateway:/', it must extract the endpoint name (the portion after 'gateway:/'), call get_gateway_litellm_config with that endpoint name, and invoke _invoke_litellm (located at mlflow.genai.judges.adapters.litellm_adapter) with keyword arguments litellm_model=config.model, api_base=config.api_base, api_key=config.api_key, extra_headers=config.extra_headers, and messages=messages.

*   When _call_llm is called with a model URI that does not start with 'gateway:/', it must call convert_mlflow_uri_to_litellm (from mlflow.metrics.genai.model_utils) with the full model URI and then invoke _invoke_litellm with litellm_model set to the converted value, api_base=None, api_key=None, extra_headers=None, and messages=messages.

*   _call_llm must return the response object returned by _invoke_litellm in both the gateway and non-gateway cases.

*   When _call_llm is called with json_mode=True, it must pass response_format={'type': 'json_object'} and include_response_format=True as keyword arguments to _invoke_litellm.

*   When _call_llm is called with a pydantic model class passed as response_format, it must pass that class directly as response_format and include_response_format=True to _invoke_litellm.

*   When _call_llm is called with a _TokenCounter instance passed as token_counter, after _invoke_litellm returns, the counter's input_tokens must be set to response.usage.prompt_tokens, output_tokens to response.usage.completion_tokens, and cost_usd to response._hidden_params['response_cost'].


*   Interface details: Type: Class
Name: GatewayLiteLLMConfig
Location: mlflow/genai/utils/gateway_utils.py
Description: A configuration object for LiteLLM calls routed through an MLflow gateway endpoint. Constructed with model, api_base, api_key, and extra_headers fields; attributes are accessible by those names.
Signature: GatewayLiteLLMConfig(model: str, api_base: str, api_key: str, extra_headers: dict)

Type: Function
Name: get_gateway_litellm_config
Location: mlflow/genai/utils/gateway_utils.py
Signature: get_gateway_litellm_config(endpoint_name: str) -> GatewayLiteLLMConfig
Description: Given an MLflow gateway endpoint name, returns a GatewayLiteLLMConfig containing the model name, base URL, API key, and extra headers needed to route a LiteLLM call through that gateway.

Type: Function
Name: _call_llm
Location: mlflow/genai/discovery/utils.py
Signature: _call_llm(model_uri: str, messages: list, json_mode: bool = False, response_format=None, token_counter=None)
Description: Calls an LLM via LiteLLM, routing through the MLflow gateway if the URI starts with "gateway:/", otherwise converting the URI with convert_mlflow_uri_to_litellm. Supports JSON mode, structured response formats (pydantic model classes), and optional token/cost tracking via a _TokenCounter instance.

Type: Class
Name: _TokenCounter
Location: mlflow/genai/discovery/utils.py
Description: Tracks cumulative token usage and cost for LLM calls. After a call routed through _call_llm, attributes input_tokens, output_tokens, and cost_usd are updated from the response.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.