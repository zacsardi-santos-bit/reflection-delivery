I'm working on the Opik Python SDK and need to add first-class support for recording assertion results from automated test suites.

*   The Opik client must expose a log_assertion_results method accepting an assertion_results list of dicts (each with 'id', 'name', 'status', and optional 'reason' and 'project_name') and an optional project_name parameter.

*   log_assertion_results must only process items where 'status' is exactly the string "passed" or "failed" (case-sensitive); items with uppercase status, boolean status, or missing status must be silently skipped.

*   log_assertion_results must skip items where 'id' is an empty string. If no valid items remain after filtering, the method must not send anything to the streamer.

*   log_assertion_results must convert valid items to AssertionResultMessage objects with source set to "sdk", using per-item project_name when present and falling back to the method-level project_name or the client's default project name otherwise.

*   log_assertion_results must wrap the batch in an AddAssertionResultsBatchMessage with entity_type set to "TRACE" and supports_batching set to False, then submit it via the client's internal streamer.

*   AssertionResultMessage must have fields: entity_id (str), project_name (str), name (str), status (str), source (str), and reason (optional str).

*   AddAssertionResultsBatchMessage must have fields: batch (list of AssertionResultMessage), entity_type (str), and supports_batching (bool, default False).

*   The log_test_result_feedback_scores function in the evaluation rest_operations module must split score results: items with category_name equal to "suite_assertion" are sent to client.log_assertion_results as dicts with id=trace_id, name, status ("passed" if value is truthy, "failed" if falsy), and reason; all other items are sent to client.log_traces_feedback_scores.

*   Items with scoring_failed=True must be excluded from both the assertion-results and feedback-scores paths. When calling log_assertion_results, project_name must be passed as a keyword argument.

*   AssertionResultsMessageProcessor must be a class in sdks/python/src/opik/message_processing/processors/assertion_results_processor.py, constructed with a rest_client parameter, and expose a process(message) method taking an AddAssertionResultsBatchMessage.

*   AssertionResultsMessageProcessor.process must call rest_client.assertion_results.store_assertions_batch(entity_type=..., assertion_results=[AssertionResultBatchItem(...)]) when _use_assertion_results_endpoint is True. It must NOT call rest_client.traces.score_batch_of_traces or rest_client.spans.score_batch_of_spans in the happy path.

*   When store_assertions_batch raises an ApiError with status_code 404 or 405, AssertionResultsMessageProcessor must set _use_assertion_results_endpoint to False and fall back to rest_client.traces.score_batch_of_traces(scores=[FeedbackScoreBatchItem(...)]) where each item has category_name="suite_assertion", value=1.0 for status "passed" and 0.0 for status "failed".

*   When store_assertions_batch raises an ApiError with a status_code other than 404 or 405 (e.g. 500), AssertionResultsMessageProcessor must re-raise the error and must NOT set _use_assertion_results_endpoint to False.

*   Once _use_assertion_results_endpoint is set to False, all subsequent calls to process must skip the assertion-results endpoint and go directly to the feedback-scores fallback without retrying the new endpoint.

*   AssertionResultBatchItem must have fields: entity_id, name, status, source, and reason (optional), imported from opik.rest_api.types.assertion_result_batch_item.


*   Interface details: Type: Method
Name: log_assertion_results
Location: sdks/python/src/opik/api_objects/opik_client.py
Signature: log_assertion_results(self, assertion_results: List[Dict], project_name: Optional[str] = None) -> None
Description: Accepts a list of assertion result dicts (each with "id", "name", "status", optional "reason", optional "project_name"), filters out invalid entries, and submits a batch message to the internal streamer.

Type: Class
Name: AssertionResultMessage
Location: sdks/python/src/opik/message_processing/messages.py
Description: Message object representing a single assertion result for a trace.
Signature: Fields: entity_id (str), project_name (str), name (str), status (str), source (str), reason (Optional[str])

Type: Class
Name: AddAssertionResultsBatchMessage
Location: sdks/python/src/opik/message_processing/messages.py
Description: Batch message containing multiple AssertionResultMessage items to be processed by the streamer.
Signature: Fields: batch (List[AssertionResultMessage]), entity_type (str), supports_batching (bool, default False)

Type: Function
Name: log_test_result_feedback_scores
Location: sdks/python/src/opik/evaluation/rest_operations.py
Signature: log_test_result_feedback_scores(client, score_results: List[ScoreResult], trace_id: str, project_name: str) -> None
Description: Routes ScoreResult items to either log_assertion_results (for items with category_name="suite_assertion") or log_traces_feedback_scores (for all others). Items with scoring_failed=True are excluded from both paths.

Type: Class
Name: AssertionResultsMessageProcessor
Location: sdks/python/src/opik/message_processing/processors/assertion_results_processor.py
Description: Message processor that sends assertion results to the dedicated backend endpoint, with automatic fallback to the legacy feedback-scores path on 404/405 errors.
Signature:
  __init__(self, rest_client) -> None
  process(self, message: AddAssertionResultsBatchMessage) -> None
  _use_assertion_results_endpoint: bool  (instance attribute, initialized to True)

Type: Class
Name: AssertionResultBatchItem
Location: sdks/python/src/opik/rest_api/types/assertion_result_batch_item.py
Description: REST API type representing a single assertion result item to send to the backend.
Signature: Fields: entity_id (str), name (str), status (str), source (str), reason (Optional[str])


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.