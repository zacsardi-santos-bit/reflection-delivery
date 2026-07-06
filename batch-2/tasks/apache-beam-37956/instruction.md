I'm working with Apache Beam's machine learning inference framework and I want to add support for Anthropic's Claude models.

*   Must implement a new module at sdks/python/apache_beam/ml/inference/anthropic_inference.py that provides AnthropicModelHandler, message_from_string, message_from_conversation, and _retry_on_appropriate_error.

*   _retry_on_appropriate_error must return True for API status errors with HTTP status codes 429, 500, and 503, and must return False for API status errors with codes 400 and 401, and must return False for any exception that is not an API status error.

*   message_from_string must accept a model name, a list of string prompts, a client, and an inference_args dict. For each prompt it must call client.messages.create with model=model_name and messages=[{"role": "user", "content": prompt}]. It must default max_tokens to 1024 when not provided in inference_args. It must forward all inference_args entries as additional keyword arguments. It must return a list of responses with the same length as the input batch.

*   message_from_conversation must accept a model name, a list of conversations (each being a list of message dicts), a client, and an inference_args dict. For each conversation it must call client.messages.create with messages=conversation. It must return a list of responses with the same length as the input batch.

*   AnthropicModelHandler must accept constructor parameters: model_name (str), request_fn (callable), api_key (optional str), system (optional str), output_config (optional dict), min_batch_size (optional int), max_batch_size (optional int).

*   AnthropicModelHandler.create_client() must create an Anthropic client by calling Anthropic(api_key=api_key) when an API key was provided, or Anthropic() (relying on the environment variable) when no API key was provided.

*   AnthropicModelHandler.request(batch, client, inference_args) must return a list of PredictionResult objects, one per input, where each PredictionResult has example set to the original input, inference set to the API response, and model_id set to the model name string.

*   AnthropicModelHandler.batch_elements_kwargs() must return a dict with min_batch_size and max_batch_size keys set to the values passed at construction.

*   When system is set on the handler, it must be forwarded as the system keyword argument to client.messages.create. If inference_args contains a 'system' key, that per-request value must override the handler-level system. If system is None and inference_args has no 'system' key, the system keyword argument must be absent from the client.messages.create call.

*   When output_config is set on the handler, it must be forwarded as the output_config keyword argument to client.messages.create. If inference_args contains an 'output_config' key, that per-request value must override the handler-level output_config. If output_config is None and inference_args has no 'output_config' key, the output_config keyword argument must be absent from the client.messages.create call.

*   AnthropicModelHandler must be compatible with RunInference in Apache Beam pipelines, producing PredictionResult objects that expose the original input example and the model response.


*   Interface details: Type: Function
Name: _retry_on_appropriate_error
Location: sdks/python/apache_beam/ml/inference/anthropic_inference.py
Signature: _retry_on_appropriate_error(error: Exception) -> bool
Description: Determines whether an API error should trigger a retry. Returns True for transient server-side errors (HTTP 429, 500, 503). Returns False for client-side errors (HTTP 400, 401) and for any exception that is not an APIStatusError.

Type: Function
Name: message_from_string
Location: sdks/python/apache_beam/ml/inference/anthropic_inference.py
Signature: message_from_string(model_name: str, batch: List[str], client, inference_args: dict) -> List
Description: Request function that wraps each string prompt as a user message and calls client.messages.create once per prompt. Passes model=model_name and messages=[{"role": "user", "content": prompt}] to the API. Uses a default max_tokens of 1024 when not provided in inference_args. All inference_args key-value pairs are forwarded as additional kwargs to client.messages.create. Returns a list of API responses, one per prompt.

Type: Function
Name: message_from_conversation
Location: sdks/python/apache_beam/ml/inference/anthropic_inference.py
Signature: message_from_conversation(model_name: str, batch: List[List[dict]], client, inference_args: dict) -> List
Description: Request function that passes a conversation (list of message dicts with "role" and "content" keys) directly to client.messages.create as the messages argument. Calls client.messages.create once per conversation in the batch. Returns a list of API responses, one per conversation.

Type: Class
Name: AnthropicModelHandler
Location: sdks/python/apache_beam/ml/inference/anthropic_inference.py
Description: A Beam ModelHandler for running inference against the Anthropic Claude API. Integrates with RunInference in standard Beam pipelines.
Signature:
  __init__(self, model_name: str, request_fn: callable, api_key: Optional[str] = None, system: Optional[str] = None, output_config: Optional[dict] = None, min_batch_size: Optional[int] = None, max_batch_size: Optional[int] = None)
  create_client(self) -> Anthropic client — creates Anthropic(api_key=api_key) when api_key is set; creates Anthropic() (reads from environment) when api_key is None
  request(self, batch, client, inference_args) -> List[PredictionResult] — calls request_fn and returns PredictionResult objects with fields example (original input), inference (API response), and model_id (model_name string)
  batch_elements_kwargs(self) -> dict — returns {'min_batch_size': self.min_batch_size, 'max_batch_size': self.max_batch_size}

Note: The module must import Anthropic at module level so it can be patched as apache_beam.ml.inference.anthropic_inference.Anthropic.

System prompt behavior: When system is set on the handler, it is forwarded as system=system to client.messages.create. If inference_args contains a 'system' key, that value overrides the handler-level system. If system is None and inference_args does not contain 'system', the system kwarg must NOT be present in the client.messages.create call.

Output config behavior: When output_config is set on the handler, it is forwarded as output_config=output_config to client.messages.create. If inference_args contains an 'output_config' key, that value overrides the handler-level output_config. If output_config is None and inference_args does not contain 'output_config', the output_config kwarg must NOT be present in the client.messages.create call.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.