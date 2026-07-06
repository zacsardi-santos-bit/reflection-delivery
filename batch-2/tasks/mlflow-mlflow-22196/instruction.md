I'm working on the MLflow GenAI scoring infrastructure and I'd like to consolidate the way scorer integrations communicate with LLM backends.

*   A new unified backend client class named ScorerLLMClient must be introduced in mlflow/genai/scorers/llm_backend.py. It accepts a model URI string and determines routing strategy automatically based on the URI format and environment configuration.

*   ScorerLLMClient must expose the properties: route (str), is_native (bool), model_name (str), provider (Optional[str]), and raw_model_name (str). The route must be one of 'databricks', 'endpoints', 'native', or 'litellm'.

*   The bare string 'databricks' must produce route='databricks', is_native=True, model_name='databricks', and provider=None. A bare model name with no '/' (e.g. 'gpt-oss-120b') must also route to 'databricks' and set model_name to the bare name.

*   The URI format 'endpoints:/my-endpoint' must produce route='endpoints', is_native=True, provider='endpoints', and model_name='endpoints/my-endpoint'.

*   A supported provider URI like 'openai:/gpt-4' with the required API key set must produce route='native', is_native=True, provider='openai', model_name='openai/gpt-4', raw_model_name='gpt-4'. Without the API key it must fall back to route='litellm'.

*   The URI 'gateway:/my-endpoint' must produce route='native', is_native=True, provider='gateway', and must call _get_provider_instance (now located in mlflow.genai.scorers.llm_backend) exactly once during construction.

*   An unknown or unsupported provider URI must produce route='litellm' and is_native=False.

*   ScorerLLMClient must provide a complete(messages: list[dict], response_format=None, num_retries: int = 0, **kwargs) -> str method. For the databricks route it must extract the system message (if any) and user message from the messages list and call call_chat_completions from mlflow.genai.judges.adapters.databricks_managed_judge_adapter with keyword arguments user_prompt, system_prompt, and model=<model_uri>.

*   For the native route, complete() must call _call_llm_provider_api (in mlflow.genai.scorers.llm_backend) with positional provider and raw model name, and keyword arguments messages=messages, eval_parameters=None, response_format=<dict_or_none>. When response_format is a Pydantic BaseModel class it must be converted to a JSON schema dict before passing; when it is already a dict it must be passed through unchanged.

*   For the litellm route, complete() must call litellm.completion and return choices[0].message.content. If litellm is not installed, it must raise MlflowException with a message matching 'not natively supported'.

*   complete() must support retry logic via the num_retries parameter: on MlflowException, it must retry up to num_retries additional times. After all retries are exhausted it must re-raise the MlflowException.

*   ScorerLLMClient must provide a complete_prompt(prompt: str) -> str convenience method that wraps the prompt string in a user message list and delegates to complete().

*   The former GatewayDeepEvalLLM and DatabricksDeepEvalLLM classes must be replaced by a single unified MlflowDeepEvalLLM class in mlflow/genai/scorers/deepeval/models.py. MlflowDeepEvalLLM must accept a ScorerLLMClient as its constructor argument and its generate() method must call _call_llm_provider_api from mlflow.genai.scorers.llm_backend with messages=[{'role': 'user', 'content': prompt}], eval_parameters=None, response_format=None. When a schema is provided, the prompt passed to the API must contain the original text and include the phrase 'Return your response as valid JSON'. get_model_name() must return the model name in 'provider/model' format.

*   create_deepeval_model(model_uri) must return a MlflowDeepEvalLLM instance for supported providers (with required API keys) and for gateway URIs, and fall back to a litellm-backed model for unsupported providers.

*   The former GatewayPhoenixModel and DatabricksPhoenixModel classes must be replaced by a single MlflowPhoenixModel class in mlflow/genai/scorers/phoenix/models.py. MlflowPhoenixModel must accept a ScorerLLMClient as its constructor argument. Its __call__() method for the databricks route must call call_chat_completions with user_prompt, system_prompt='', and model=<model_uri>. For native/gateway routes it must use _call_llm_provider_api from mlflow.genai.scorers.llm_backend. get_model_name() must return the model name in 'provider/model' format.

*   create_phoenix_model(model_uri) must return a MlflowPhoenixModel instance for all supported backends (databricks, databricks endpoint, openai and other supported providers, gateway).

*   The former GatewayRagasLLM and DatabricksRagasLLM classes must be replaced by a single MlflowRagasLLM class in mlflow/genai/scorers/ragas/models.py. MlflowRagasLLM must accept a ScorerLLMClient as its constructor argument. For the databricks route its generate() method must call call_chat_completions with model=<model_uri>. get_model_name() must return the model name in 'provider/model' format.

*   create_ragas_model(model_uri) must return a MlflowRagasLLM instance for all known backends (databricks, databricks endpoint, openai, gateway).

*   The _create_gateway_provider function in mlflow/genai/scorers/trulens/models.py must accept a ScorerLLMClient instance instead of separate provider and model string arguments. Internally it must use _call_llm_provider_api from mlflow.genai.scorers.llm_backend.

*   The _call_llm_provider_api and _get_provider_instance functions must be centralized in mlflow/genai/scorers/llm_backend.py. Tests for trulens, phoenix, ragas, and deepeval models must patch these functions at mlflow.genai.scorers.llm_backend, not at the individual scorer module paths.

*   The call_chat_completions function for the databricks route must be accessed from mlflow.genai.judges.adapters.databricks_managed_judge_adapter (not from the individual scorer model modules).

*   The invoke_model_without_tracing utility must use ScorerLLMClient.complete (at path mlflow.genai.scorers.llm_backend.ScorerLLMClient.complete) instead of a private _invoke_llm function. When inference parameters such as temperature are passed, they must be forwarded as keyword arguments to complete() alongside response_format and num_retries.


*   Interface details: Type: Class
Name: ScorerLLMClient
Location: mlflow/genai/scorers/llm_backend.py
Description: Unified LLM backend client that handles routing, retry logic, and dispatch for all scorer integrations. Constructor accepts a model URI string and determines the appropriate routing strategy.
Signature: ScorerLLMClient(model_uri: str)
Properties:
  - route: str — one of "databricks", "endpoints", "native", "litellm"
  - is_native: bool — True for "databricks", "endpoints", "native" routes; False for "litellm"
  - model_name: str — formatted as "provider/model" (e.g. "openai/gpt-4") or bare name (e.g. "databricks")
  - provider: Optional[str] — the provider prefix from the URI (e.g. "openai"), or None for bare "databricks"
  - raw_model_name: str — the model part without provider prefix (e.g. "gpt-4" from "openai:/gpt-4")
Methods:
  - complete(messages: list[dict], response_format=None, num_retries: int = 0, **kwargs) -> str
  - complete_prompt(prompt: str) -> str

Route determination rules:
  - "databricks" (bare string) → route="databricks", is_native=True, model_name="databricks", provider=None
  - Bare model name with no "/" (e.g. "gpt-oss-120b") → route="databricks", is_native=True, model_name=<bare_name>
  - "endpoints:/my-endpoint" → route="endpoints", is_native=True, provider="endpoints", model_name="endpoints/my-endpoint"
  - "openai:/gpt-4" with OPENAI_API_KEY set → route="native", is_native=True, provider="openai", model_name="openai/gpt-4", raw_model_name="gpt-4"
  - "openai:/gpt-4" without OPENAI_API_KEY → route="litellm"
  - "gateway:/my-endpoint" → route="native", is_native=True, provider="gateway" (calls _get_provider_instance)
  - Unknown provider without required API key → route="litellm", is_native=False

complete() behavior:
  - databricks route: extracts user and system messages from the messages list, calls databricks_managed_judge_adapter.call_chat_completions(user_prompt=..., system_prompt=..., model=<model_uri>)
  - native route: calls _call_llm_provider_api(provider, raw_model_name, messages=messages, eval_parameters=None, response_format=<dict_or_none>)
  - litellm route: calls litellm.completion(...), returns choices[0].message.content
  - If litellm is not installed: raises MlflowException matching "not natively supported"
  - Retries up to num_retries times on MlflowException; raises on exhaustion
  - If response_format is a Pydantic BaseModel class: converts to JSON schema dict before passing to _call_llm_provider_api
  - If response_format is already a dict: passes through unchanged

complete_prompt() behavior:
  - Wraps the prompt string as [{"role": "user", "content": prompt}] and calls complete()


Type: Class
Name: MlflowDeepEvalLLM
Location: mlflow/genai/scorers/deepeval/models.py
Description: Unified DeepEval LLM adapter (replaces the former GatewayDeepEvalLLM and DatabricksDeepEvalLLM). Takes a ScorerLLMClient instance.
Signature: MlflowDeepEvalLLM(client: ScorerLLMClient)
Methods:
  - generate(prompt: str, schema=None) -> str — calls _call_llm_provider_api from mlflow.genai.scorers.llm_backend with messages=[{"role": "user", "content": prompt}], eval_parameters=None, response_format=None; when schema is given, enriches prompt to include "Return your response as valid JSON" and returns a parsed schema instance
  - get_model_name() -> str — returns "provider/model" format (e.g. "openai/gpt-4", "anthropic/claude-3", "gateway/my-endpoint")


Type: Class
Name: MlflowPhoenixModel
Location: mlflow/genai/scorers/phoenix/models.py
Description: Unified Phoenix model adapter (replaces former GatewayPhoenixModel and DatabricksPhoenixModel). Takes a ScorerLLMClient instance.
Signature: MlflowPhoenixModel(backend: ScorerLLMClient)
Methods:
  - __call__(prompt) -> str — invokes the backend; for databricks route calls call_chat_completions(user_prompt=..., system_prompt="", model=<model_uri>); for native route calls _call_llm_provider_api from mlflow.genai.scorers.llm_backend
  - get_model_name() -> str — returns "provider/model" format


Type: Class
Name: MlflowRagasLLM
Location: mlflow/genai/scorers/ragas/models.py
Description: Unified Ragas LLM adapter (replaces former GatewayRagasLLM and DatabricksRagasLLM). Takes a ScorerLLMClient instance.
Signature: MlflowRagasLLM(backend: ScorerLLMClient)
Methods:
  - generate(prompt: str, response_model=None) -> object — dispatches via the ScorerLLMClient; for databricks route calls call_chat_completions with model=<model_uri>; returns parsed response_model instance if provided
  - get_model_name() -> str — returns "provider/model" format


Type: Function
Name: create_deepeval_model
Location: mlflow/genai/scorers/deepeval/models.py
Description: Factory that returns MlflowDeepEvalLLM for supported/gateway URIs, falls back to litellm for unsupported providers.
Signature: create_deepeval_model(model_uri: str) -> MlflowDeepEvalLLM


Type: Function
Name: create_phoenix_model
Location: mlflow/genai/scorers/phoenix/models.py
Description: Factory that always returns MlflowPhoenixModel for known backends.
Signature: create_phoenix_model(model_uri: str) -> MlflowPhoenixModel


Type: Function
Name: create_ragas_model
Location: mlflow/genai/scorers/ragas/models.py
Description: Factory that returns MlflowRagasLLM for all known backends.
Signature: create_ragas_model(model_uri: str) -> MlflowRagasLLM


Type: Function
Name: _create_gateway_provider
Location: mlflow/genai/scorers/trulens/models.py
Description: Creates a TruLens LLMProvider backed by a ScorerLLMClient. Signature changed from (provider, model) to accepting a ScorerLLMClient instance.
Signature: _create_gateway_provider(backend: ScorerLLMClient) -> LLMProvider


Type: Function
Name: _call_llm_provider_api
Location: mlflow/genai/scorers/llm_backend.py
Description: Low-level function to call a native LLM provider API. Previously duplicated in individual scorer model modules; now centralized in llm_backend.
Signature: _call_llm_provider_api(provider: str, model: str, messages: list[dict], eval_parameters=None, response_format=None) -> str


Type: Function
Name: _get_provider_instance
Location: mlflow/genai/scorers/llm_backend.py
Description: Returns a provider instance for gateway URIs. Previously duplicated in individual scorer model modules; now centralized in llm_backend.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.