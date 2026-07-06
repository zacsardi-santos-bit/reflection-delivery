I'm working with MLflow's tracing system and running into an issue with how LLM call costs are computed.

*   A boolean constant named IS_TRACING_SDK_ONLY must be defined and exported from the mlflow.version module, indicating whether MLflow is running in tracing-SDK-only mode (i.e., the lightweight tracing package is installed without the full MLflow package).

*   A function named should_compute_cost_client_side must be defined in mlflow.tracing.utils and must return True when the active tracking URI is a Databricks URI, and False otherwise.

*   When a LiveSpan ends, the client-side cost attribute computation (set_span_cost_attribute) must only be invoked when should_compute_cost_client_side() returns True; it must not be called for non-Databricks backends.

*   When a span ends and the tracking URI is NOT a Databricks URI, the trace's cost information (trace.info.cost) must still be populated with input_cost, output_cost, and total_cost fields — computed server-side rather than client-side.

*   The mock_litellm_cost test fixture must handle ImportError for litellm gracefully by yielding None when litellm is not installed, and must use create=True in mock.patch to support environments where litellm is absent.

*   The model name attribute extracted during span translation must be stored as a raw string value rather than as a JSON-encoded string; any JSON decoding of the raw attribute value should happen during extraction, not during storage.


*   Interface details: Type: Constant
Name: IS_TRACING_SDK_ONLY
Location: mlflow/version.py
Signature: IS_TRACING_SDK_ONLY: bool
Description: Boolean constant that is True when running in tracing-SDK-only mode (the lightweight tracing package installed without the full MLflow package), and False otherwise. Imported by many integration test files to conditionally skip LLM cost assertions.

Type: Function
Name: should_compute_cost_client_side
Location: mlflow/tracing/utils/__init__.py
Signature: should_compute_cost_client_side() -> bool
Description: Returns True only when the active tracking URI is a Databricks URI, indicating that LLM cost should be computed on the client side. Returns False for non-Databricks backends, where cost is computed server-side during span ingestion. Used to gate all calls to set_span_cost_attribute in mlflow/entities/span.py, mlflow/haystack/autolog.py, mlflow/semantic_kernel/autolog.py, mlflow/tracing/processor/base_mlflow.py, and mlflow/tracing/processor/inference_table.py.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.