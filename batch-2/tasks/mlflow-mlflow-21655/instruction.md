I'm working on the GenAI issue discovery pipeline and need to add a few improvements.

*   The `_IdentifiedIssue` Pydantic model in `mlflow/genai/discovery/entities.py` must include a required `categories` field of type `list[str]` (no default value).

*   The `DiscoverIssuesResult` dataclass in `mlflow/genai/discovery/entities.py` must include a `total_cost_usd` field of type `float | None`. When no traces are provided, it must be `0.0`. When LLM calls are made during discovery, it must be a positive float reflecting the accumulated cost.

*   The `summarize_cluster` function in `mlflow/genai/discovery/clustering.py` must accept a `categories: list[str]` parameter (positioned after `model`). After the LLM returns an issue, any categories in the result that are not present in the provided `categories` list must be removed. The function must internally route LLM calls through `mlflow.genai.discovery.clustering._call_llm`.

*   The `recluster_singletons` function in `mlflow/genai/discovery/clustering.py` must accept a `categories: list[str]` keyword parameter and propagate it to all internal calls to `summarize_cluster`.

*   A new function `build_cluster_summary_prompt(categories: list[str]) -> str` must be added to `mlflow/genai/discovery/constants.py`. The returned string must include each category name from the list and must contain the phrase 'ONLY include categories from this list'.

*   The `discover_issues` function in `mlflow/genai/discovery/pipeline.py` must accept an optional `run_id: str | None = None` parameter. When provided, the result's `triage_run_id` must equal the supplied run ID, and the run must remain accessible via `mlflow.get_run` after discovery completes.

*   The `discover_issues` function must accept a `categories: list[str]` parameter (with a default value). This list must be passed through to `summarize_cluster` calls so that only valid categories appear in discovered issues.

*   The MLflow run started by `discover_issues` must be tagged with `MLFLOW_RUN_TYPE` set to `MLFLOW_RUN_TYPE_ISSUE_DETECTION`, where both constants are importable from `mlflow.utils.mlflow_tags`.

*   The `build_summary` function in `mlflow/genai/discovery/utils.py` must NOT prefix its output with `##`. When no issues are found, it must return exactly `'Analyzed {n} traces. No issues found.'`. When issues are present, the output must contain `'Analyzed **{n}** traces. Found **{m}** issues:\n'`, issue sections formatted as `'### {i}. {name}'`, and multiple root causes joined with `'; '`.

*   A `_TokenCounter` class must be available in `mlflow/genai/discovery/utils.py` with attributes `input_tokens` (int, initial 0), `output_tokens` (int, initial 0), and `cost_usd` (float, initial 0.0). Its `track(response)` method must accumulate `response.usage.prompt_tokens` into `input_tokens`, `response.usage.completion_tokens` into `output_tokens`, and `response._hidden_params['response_cost']` into `cost_usd`.

*   A `group_traces_by_session` function must be available in `mlflow/genai/discovery/utils.py`. It must accept a list of traces and return a dict. Traces with a session ID must be grouped under that session ID as the key. Traces without a session ID must each be placed in a separate group keyed by their `trace_id`. Within each group, traces must be sorted by timestamp in ascending order.

*   The `_start_run_or_reuse_active_run` context manager in `mlflow/models/evaluation/base.py` must yield a run object (not a run ID string). The yielded object must expose `.info.run_id` to retrieve the run's ID.

*   Internal LLM calls made during trace annotation (in `_annotate_issue_traces`) must route through `mlflow.genai.judges.adapters.litellm_adapter._invoke_litellm` rather than calling `litellm.completion` directly.

*   The `MLFLOW_RUN_TYPE_ISSUE_DETECTION` constant must exist in `mlflow/utils/mlflow_tags.py` and must be usable as a tag value to identify issue detection runs.


*   Interface details: Type: Class
Name: _IdentifiedIssue
Location: mlflow/genai/discovery/entities.py
Description: Pydantic model representing a discovered issue cluster. Must include a new required `categories` field.
Signature: categories: list[str]  (required Pydantic field, no default)

Type: Class
Name: DiscoverIssuesResult
Location: mlflow/genai/discovery/entities.py
Description: Dataclass for the result of `discover_issues`. Must include a new `total_cost_usd` field.
Signature: total_cost_usd: float | None = None

Type: Function
Name: summarize_cluster
Location: mlflow/genai/discovery/clustering.py
Description: Summarizes a cluster of conversation analyses into an identified issue. New `categories` parameter is required; the returned issue's categories are filtered to only those present in the provided list.
Signature: summarize_cluster(cluster_label_indices: list[int], analyses: list[_ConversationAnalysis], model: str, categories: list[str], label_to_analysis: list[int] | None = None, token_counter: _TokenCounter | None = None) -> _IdentifiedIssue

Type: Function
Name: recluster_singletons
Location: mlflow/genai/discovery/clustering.py
Description: Re-clusters singleton issues. New `categories` keyword parameter is required and forwarded to `summarize_cluster`.
Signature: recluster_singletons(singletons: list[_IdentifiedIssue], labels: dict, analyses: list[_ConversationAnalysis], model: str, max_issues: int, categories: list[str], token_counter: _TokenCounter | None = None) -> list[_IdentifiedIssue]

Type: Function
Name: build_cluster_summary_prompt
Location: mlflow/genai/discovery/constants.py
Description: Builds the LLM system prompt for cluster summarization, incorporating the valid category list. The returned string must include each category name and must contain the phrase "ONLY include categories from this list".
Signature: build_cluster_summary_prompt(categories: list[str]) -> str

Type: Function
Name: discover_issues
Location: mlflow/genai/discovery/pipeline.py
Description: Main entry point for issue discovery. New `run_id` parameter allows reusing an existing tracking run; new `categories` parameter controls which issue categories are recognized. The run is tagged with MLFLOW_RUN_TYPE set to MLFLOW_RUN_TYPE_ISSUE_DETECTION.
Signature: discover_issues(..., run_id: str | None = None, categories: list[str] = DEFAULT_CATEGORIES) -> DiscoverIssuesResult

Type: Function
Name: build_summary
Location: mlflow/genai/discovery/utils.py
Description: Builds a human-readable summary of discovered issues. Output must NOT start with "##". For zero issues: returns "Analyzed {n} traces. No issues found." For issues: starts with "Analyzed **{n}** traces. Found **{m}** issues:\n", uses "### {i}. {name}" for each issue section, and joins multiple root causes with "; ".
Signature: build_summary(issues: list[Issue], total_traces: int) -> str

Type: Class
Name: _TokenCounter
Location: mlflow/genai/discovery/utils.py
Description: Utility class that accumulates LLM token usage and cost across multiple calls. Initialized with all counters at zero.
Signature:
  __init__(self) -> None  (sets input_tokens=0, output_tokens=0, cost_usd=0.0)
  track(self, response) -> None  (adds response.usage.prompt_tokens to input_tokens, response.usage.completion_tokens to output_tokens, response._hidden_params["response_cost"] to cost_usd)

Type: Function
Name: group_traces_by_session
Location: mlflow/genai/discovery/utils.py
Description: Groups a list of traces by session ID. Traces with a session ID are grouped under that ID; traces without a session ID each get their own group keyed by their trace_id. Within each group, traces are sorted by timestamp in ascending order.
Signature: group_traces_by_session(traces: list) -> dict[str, list]

Type: ContextManager
Name: _start_run_or_reuse_active_run
Location: mlflow/models/evaluation/base.py
Description: Context manager that starts a new MLflow run or reuses the currently active run. Must yield a run object (not a run ID string) so callers can access run.info.run_id on the yielded value.
Signature: _start_run_or_reuse_active_run() -> ContextManager[ActiveRun]

Type: Constant
Name: MLFLOW_RUN_TYPE_ISSUE_DETECTION
Location: mlflow/utils/mlflow_tags.py
Description: String constant used as the value for the MLFLOW_RUN_TYPE tag on runs created by the issue discovery pipeline. Must be importable as `from mlflow.utils.mlflow_tags import MLFLOW_RUN_TYPE, MLFLOW_RUN_TYPE_ISSUE_DETECTION`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.