I'm working with MLflow's LLM judge system and need to use it in an environment where all outbound API calls must go through a corporate proxy server, and also pass custom authentication headers to the LLM provider.

*   make_judge() must accept optional base_url (string) and extra_headers (dict[str, str]) parameters and store them as private attributes _base_url and _extra_headers on the returned InstructionsJudge.

*   make_judge() must raise MlflowException with a message matching 'base_url must be a string' when base_url is not a string (e.g., an integer).

*   make_judge() must raise MlflowException with a message matching 'extra_headers must be a dictionary' when extra_headers is not a dict (e.g., a list).

*   make_judge() must raise MlflowException with a message matching 'must all be strings' when any extra_headers value is not a string (e.g., an integer).

*   InstructionsJudge.__repr__() must include base_url='<url>' when _base_url is set, with credentials (user:pass@), query parameters, and URL fragments stripped from the displayed URL. When _base_url is None, 'base_url' must not appear in repr().

*   InstructionsJudge.__repr__() must include extra_headers= with header key names visible but header values must NOT be shown (for security). When _extra_headers is None, 'extra_headers' must not appear in repr().

*   InstructionsJudge.model_dump() must exclude base_url and extra_headers from the serialized data (specifically from the 'instructions_judge_pydantic_data' key). After round-trip deserialization via Scorer.model_validate(), the judge's _base_url and _extra_headers must both be None.

*   When an InstructionsJudge is called (evaluated), it must pass base_url and extra_headers through to invoke_judge_model() as keyword arguments.

*   invoke_judge_model() must accept optional base_url and extra_headers keyword arguments. When model_uri uses an 'endpoints' provider and either base_url or extra_headers is provided, it must raise MlflowException with a message matching 'not supported for deployment endpoints'.

*   AdapterInvocationInput must have optional base_url (str | None, default None) and extra_headers (dict[str, str] | None, default None) fields.

*   DatabricksManagedJudgeAdapter.invoke() must raise MlflowException with a message matching 'base_url and extra_headers are not supported' when AdapterInvocationInput.base_url is set, when extra_headers is set, or when both are set.

*   _invoke_litellm() must accept an optional extra_headers parameter. When provided, extra_headers must be passed as the extra_headers kwarg to litellm.completion(). When not provided, the extra_headers key must be absent from the litellm.completion() call kwargs.

*   _invoke_litellm_and_handle_tools() must accept optional base_url and extra_headers parameters. When base_url is provided and provider is not 'gateway', it must be passed as api_base to litellm.completion() and api_key must not be included in the call. When extra_headers is provided, it must be passed through to litellm.completion(). When provider is 'gateway', base_url must be ignored and the gateway's own api_base must be used instead.

*   LiteLLMAdapter.invoke() must raise MlflowException with a message matching 'base_url and extra_headers are not supported' when AdapterInvocationInput has base_url or extra_headers set and the model provider is 'databricks' or 'endpoints'.

*   The register-llm-judge CLI command must accept --base-url (string) and --extra-headers (JSON string) options. On success both should yield exit code 0 and output containing 'Successfully created and registered'. base_url must NOT be persisted in the registered scorer. For --extra-headers: non-JSON input must produce exit code != 0 and output containing 'Invalid JSON'; valid JSON that is not a dict must produce exit code != 0 and output containing 'Expected a JSON object'; a dict with non-string values must produce exit code != 0 and output containing 'must all be strings'.


*   Interface details: Type: Function
Name: make_judge
Location: mlflow/genai/judges/make_judge.py
Signature: make_judge(name: str, instructions: str, model: str | None = None, description: str | None = None, feedback_value_type: Any = None, inference_params: dict[str, Any] | None = None, base_url: str | None = None, extra_headers: dict[str, str] | None = None) -> InstructionsJudge
Description: Creates an LLM judge. Now accepts base_url (optional proxy URL) and extra_headers (optional dict of HTTP headers). Raises MlflowException with message matching "base_url must be a string" if base_url is not a string. Raises MlflowException matching "extra_headers must be a dictionary" if extra_headers is not a dict. Raises MlflowException matching "must all be strings" if extra_headers values are not strings.

Type: Class
Name: InstructionsJudge
Location: mlflow/genai/judges/instructions_judge/__init__.py (or mlflow/genai/judges/make_judge.py)
Description: Judge class returned by make_judge(). Has private attributes _base_url (str | None) and _extra_headers (dict[str, str] | None). repr() includes base_url='<url>' when set (credentials, query params, and fragments stripped from URL; e.g. "http://user:pass@host:port/path?q=1#f" becomes "http://host:port/path"). repr() includes extra_headers= with header keys only (values are omitted for security). When _base_url is None, repr() does NOT include "base_url". When _extra_headers is None, repr() does NOT include "extra_headers". model_dump() excludes both base_url and extra_headers from the serialized pydantic data (key "instructions_judge_pydantic_data"). When deserialized via Scorer.model_validate(), _base_url and _extra_headers are None. When called as a function, passes base_url and extra_headers through to invoke_judge_model().

Type: Function
Name: invoke_judge_model
Location: mlflow/genai/judges/utils/invocation_utils.py
Signature: invoke_judge_model(model_uri: str, prompt: str, assessment_name: str, trace: Optional[Trace] = None, num_retries: int = 3, response_format: type | None = None, use_case: str | None = None, inference_params: dict[str, Any] | None = None, base_url: str | None = None, extra_headers: dict[str, str] | None = None) -> Feedback
Description: Invokes a judge model. New parameters: base_url and extra_headers. When model_uri starts with "endpoints:/" and base_url or extra_headers is provided, raises MlflowException with message matching "not supported for deployment endpoints". Also now passes proxy_url and extra_headers to its internal native-provider call.

Type: Class
Name: AdapterInvocationInput
Location: mlflow/genai/judges/adapters/base_adapter.py
Description: Dataclass for adapter invocation parameters. Now has two new optional fields: base_url: str | None = None and extra_headers: dict[str, str] | None = None.
Signature: AdapterInvocationInput(prompt: str, assessment_name: str, model_uri: str, trace: Optional[Trace] = None, num_retries: int = 3, base_url: str | None = None, extra_headers: dict[str, str] | None = None)

Type: Class
Name: DatabricksManagedJudgeAdapter
Location: mlflow/genai/judges/adapters/databricks_managed_judge_adapter.py
Description: Adapter for Databricks managed judges. invoke() must raise MlflowException with message matching "base_url and extra_headers are not supported" when AdapterInvocationInput.base_url is set, or when AdapterInvocationInput.extra_headers is set, or when both are set.
Signature: invoke(input_params: AdapterInvocationInput) -> Any

Type: Function
Name: _invoke_litellm
Location: mlflow/genai/judges/adapters/litellm_adapter.py
Signature: _invoke_litellm(litellm_model: str, messages: list, tools: list, num_retries: int, response_format: Any, include_response_format: bool, inference_params: dict | None = None, api_base: str | None = None, api_key: str | None = None, extra_headers: dict[str, str] | None = None) -> Any
Description: Invokes litellm.completion. New parameter: extra_headers. When extra_headers is provided, passes it to litellm.completion() as the extra_headers kwarg. When extra_headers is not provided, the extra_headers key must NOT be present in the litellm.completion() kwargs.

Type: Function
Name: _invoke_litellm_and_handle_tools
Location: mlflow/genai/judges/adapters/litellm_adapter.py
Signature: _invoke_litellm_and_handle_tools(provider: str, model_name: str, messages: list, trace: Optional[Trace] = None, num_retries: int = 3, response_format: Any = None, inference_params: dict | None = None, base_url: str | None = None, extra_headers: dict[str, str] | None = None) -> Any
Description: Handles litellm invocation with tool call loop. When base_url is provided and provider is not "gateway", passes it as api_base to litellm.completion() and does NOT pass api_key. When extra_headers is provided, passes it through to litellm.completion(). When provider is "gateway", ignores base_url and uses the gateway's own api_base instead.

Type: Class
Name: LiteLLMAdapter
Location: mlflow/genai/judges/adapters/litellm_adapter.py
Description: Adapter for LiteLLM-based judges. invoke() must raise MlflowException with message matching "base_url and extra_headers are not supported" when AdapterInvocationInput has base_url or extra_headers set and the model_uri provider is "databricks" or "endpoints".
Signature: invoke(input_params: AdapterInvocationInput) -> Any

Type: CLI Command
Name: register-llm-judge
Location: mlflow/cli/scorers.py
Description: CLI command to register an LLM judge. New options: --base-url (string, optional) and --extra-headers (JSON string, optional). --extra-headers must be a JSON object with string keys and string values. Invalid JSON → exit code != 0, output contains "Invalid JSON". Valid JSON but not a dict → exit code != 0, output contains "Expected a JSON object". Dict with non-string values → exit code != 0, output contains "must all be strings". On success: exit code 0, output contains "Successfully created and registered". base_url is NOT persisted in the registered scorer.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.