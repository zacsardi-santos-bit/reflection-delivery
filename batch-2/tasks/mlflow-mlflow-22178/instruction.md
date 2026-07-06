I'm working on the judge adapter framework and I've noticed that telemetry recording for Databricks-backed models is duplicated in every adapter subclass.

*   BaseJudgeAdapter must implement invoke() as a concrete template method that internally calls self._invoke(input_params), not as an abstract method. Subclasses must implement _invoke() as the abstract extension point for the actual invocation logic.

*   When invoke() succeeds and the model_uri has a provider prefix of 'databricks' or 'endpoints' and contains a model name (e.g. 'databricks:/model-name'), it must call _record_judge_model_usage_success_databricks_telemetry from mlflow.genai.judges.utils.telemetry_utils with keyword arguments: request_id (from AdapterInvocationOutput), model_provider (the URI scheme, e.g. 'databricks' or 'endpoints'), endpoint_name (the model name portion after ':/'), num_prompt_tokens (from AdapterInvocationOutput), and num_completion_tokens (from AdapterInvocationOutput).

*   When the model_uri does not have a 'databricks' or 'endpoints' provider prefix (e.g. 'openai:/gpt-4'), invoke() must NOT call _record_judge_model_usage_success_databricks_telemetry.

*   When invoke() calls the success telemetry function and it raises any exception, invoke() must silently suppress the telemetry error and return the AdapterInvocationOutput normally without re-raising.

*   When _invoke() raises a MlflowException and the model_uri is a Databricks provider (prefix 'databricks' or 'endpoints' with a model name), invoke() must call _record_judge_model_usage_failure_databricks_telemetry from mlflow.genai.judges.utils.telemetry_utils with at least model_provider and endpoint_name keyword arguments, then re-raise the original MlflowException.

*   When _invoke() raises a non-MlflowException error (e.g. KeyError), invoke() must NOT call _record_judge_model_usage_failure_databricks_telemetry, and must propagate the exception normally.

*   When the failure telemetry function itself raises an exception, invoke() must still re-raise the original MlflowException without suppression.

*   A model_uri that is the bare string 'databricks' (without a ':/model-name' component) must NOT trigger telemetry — both success and failure telemetry must be skipped for bare provider-only URIs.

*   AdapterInvocationInput and AdapterInvocationOutput must be importable from mlflow.genai.judges.adapters.base_adapter alongside BaseJudgeAdapter.

*   GatewayAdapter._invoke() must return an AdapterInvocationOutput that includes request_id, num_prompt_tokens, and num_completion_tokens populated from the underlying tool invocation output, not just the feedback field.

*   The telemetry functions _record_judge_model_usage_success_databricks_telemetry and _record_judge_model_usage_failure_databricks_telemetry must be importable from mlflow.genai.judges.utils.telemetry_utils (not from mlflow.genai.judges.adapters.litellm_adapter).


*   Interface details: Type: Class
Name: BaseJudgeAdapter
Location: mlflow/genai/judges/adapters/base_adapter.py
Description: Abstract base class for judge model adapters. Uses a template method pattern where `invoke()` is a concrete method that handles telemetry and calls `_invoke()` which is the abstract extension point.
Signature:
  invoke(self, input_params: AdapterInvocationInput) -> AdapterInvocationOutput  # concrete template method
  _invoke(self, input_params: AdapterInvocationInput) -> AdapterInvocationOutput  # abstract, must be implemented by subclasses
  is_applicable(cls, model_uri, prompt) -> bool  # abstract classmethod

Type: Class
Name: AdapterInvocationInput
Location: mlflow/genai/judges/adapters/base_adapter.py
Description: Dataclass representing input to a judge adapter invocation.
Fields: model_uri (str), prompt (str or list), assessment_name (str), trace (optional), base_url (optional), extra_headers (optional)

Type: Class
Name: AdapterInvocationOutput
Location: mlflow/genai/judges/adapters/base_adapter.py
Description: Dataclass representing the output of a judge adapter invocation.
Fields: feedback (Feedback), request_id (optional str or None), num_prompt_tokens (optional int or None), num_completion_tokens (optional int or None), cost (optional float or None)

Type: Function
Name: _record_judge_model_usage_success_databricks_telemetry
Location: mlflow/genai/judges/utils/telemetry_utils.py
Signature: _record_judge_model_usage_success_databricks_telemetry(request_id, model_provider, endpoint_name, num_prompt_tokens, num_completion_tokens)
Description: Records successful Databricks judge model usage telemetry. Called by BaseJudgeAdapter.invoke() when invocation succeeds for databricks or endpoints model providers.

Type: Function
Name: _record_judge_model_usage_failure_databricks_telemetry
Location: mlflow/genai/judges/utils/telemetry_utils.py
Signature: _record_judge_model_usage_failure_databricks_telemetry(model_provider, endpoint_name, ...)
Description: Records failed Databricks judge model usage telemetry. Called by BaseJudgeAdapter.invoke() when a MlflowException is raised for databricks or endpoints model providers.

Type: Class
Name: GatewayAdapter
Location: mlflow/genai/judges/adapters/gateway_adapter.py
Description: Judge adapter for gateway/native model providers. The _invoke() method (renamed from invoke()) must now return an AdapterInvocationOutput populated with request_id, num_prompt_tokens, and num_completion_tokens from the underlying invocation output.
Signature: _invoke(self, input_params: AdapterInvocationInput) -> AdapterInvocationOutput


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.