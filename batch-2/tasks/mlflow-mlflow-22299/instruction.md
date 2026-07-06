I'm working on adding Google ADK integration as native MLflow scorers.

*   ToolTrajectory must accept match_type (default 'EXACT') and threshold (default 0.5) constructor parameters. When called successfully, it must return a Feedback object with name='ToolTrajectory', metadata containing 'score' (the raw numeric score), 'threshold' (the configured threshold), and FRAMEWORK_METADATA_KEY set to 'google_adk', and source=AssessmentSource(source_type=AssessmentSourceType.CODE, source_id='google_adk/ToolTrajectory').

*   ToolTrajectory must return Feedback with value=CategoricalRating.YES when the evaluator score meets or exceeds the threshold, and CategoricalRating.NO when it does not.

*   ToolTrajectory must validate that expectations contains an 'expected_tool_calls' key. If expectations is None or lacks that key, it must return a Feedback object with a non-None error whose string representation contains 'expected_tool_calls', and metadata equal to {FRAMEWORK_METADATA_KEY: 'google_adk'}, with the same source AssessmentSource.

*   ToolTrajectory must handle exceptions raised by the underlying evaluator by returning a Feedback object with a non-None error containing the exception message, and metadata equal to {FRAMEWORK_METADATA_KEY: 'google_adk'}.

*   ToolTrajectory must pass threshold and match_type to _create_trajectory_evaluator(threshold, match_type). It must accept match_type values 'EXACT', 'IN_ORDER', and 'ANY_ORDER'.

*   When ToolTrajectory is called with a trace argument and expectations does not contain 'actual_tool_calls', it must extract actual tool calls from trace spans of type SpanType.TOOL and use them to populate the actual invocation's intermediate_data.tool_uses.

*   When expectations contains an 'actual_tool_calls' key, ToolTrajectory must use that list to populate actual_invocations instead of extracting from the trace.

*   ResponseMatch must accept a threshold constructor parameter. When called successfully, it must return a Feedback object with name='ResponseMatch', metadata containing 'score', 'threshold', and FRAMEWORK_METADATA_KEY='google_adk', and source=AssessmentSource(source_type=AssessmentSourceType.CODE, source_id='google_adk/ResponseMatch'). Value must be CategoricalRating.YES if score >= threshold, else CategoricalRating.NO.

*   ResponseMatch must look for the reference text in expectations['expected_response'] first, then fall back to expectations['reference'], then expectations['context']. If none of these are present and expectations is None, it must return Feedback with a non-None error whose string representation contains 'expected_response'.

*   ResponseMatch must handle evaluator exceptions by returning a Feedback object with a non-None error containing the exception message and metadata equal to {FRAMEWORK_METADATA_KEY: 'google_adk'}.

*   Both ToolTrajectory and ResponseMatch must have a kind property returning ScorerKind.THIRD_PARTY.

*   Both ToolTrajectory and ResponseMatch must raise an exception with a message matching 'not supported for third-party scorers' when any of register(), start(), update(), stop(), or align() is called.

*   The scorer's name attribute must equal 'ToolTrajectory' for ToolTrajectory and 'ResponseMatch' for ResponseMatch (i.e., scorer.name must appear within source_id 'google_adk/ToolTrajectory' and 'google_adk/ResponseMatch' respectively).

*   get_scorer must return a ToolTrajectory instance for metric_name='ToolTrajectory' and a ResponseMatch instance for metric_name='ResponseMatch', passing all **kwargs to the constructor. For any other metric_name, it must raise an exception with message matching 'Unknown Google ADK metric'.

*   map_scorer_inputs_to_invocation must return a (actual, expected) tuple. actual.user_content.parts[0].text must equal the inputs string; actual.final_response.parts[0].text must equal the outputs string. When trace spans of type TOOL are present (and not overridden), actual.intermediate_data.tool_uses must contain entries with the corresponding name and args. expected.intermediate_data.tool_uses must be populated from expectations['expected_tool_calls']; expected.final_response.parts[0].text from expectations['expected_response'] (or 'reference' or 'context'). When no expectations are provided, expected.intermediate_data and expected.final_response must both be None.

*   _extract_actual_tool_calls must return a list of dicts with 'name' and 'args' keys. Priority order: expectations['actual_tool_calls'] (if present) > tool calls from trace TOOL spans > empty list. Returns [] when both expectations and trace are None or empty.


*   Interface details: Type: Class
Name: ToolTrajectory
Location: mlflow/genai/scorers/google_adk/__init__.py
Description: A scorer that uses Google ADK's trajectory evaluator to compare actual versus expected tool calls. Callable as a scorer with inputs, outputs, expectations (dict with "expected_tool_calls" key), and optional trace. Returns a Feedback object. Has a `kind` property returning ScorerKind.THIRD_PARTY. Methods register(), start(), update(), stop(), align() all raise an exception with message matching "not supported for third-party scorers". The scorer's name attribute is "ToolTrajectory". The source is AssessmentSource(source_type=AssessmentSourceType.CODE, source_id="google_adk/ToolTrajectory").
Signature: ToolTrajectory(match_type="EXACT", threshold=0.5)

Type: Class
Name: ResponseMatch
Location: mlflow/genai/scorers/google_adk/__init__.py
Description: A scorer that uses Google ADK's ROUGE evaluator to measure text similarity between actual and expected responses. Callable as a scorer with inputs, outputs, expectations (dict with "expected_response", "reference", or "context" key), and optional trace. Returns a Feedback object. Has a `kind` property returning ScorerKind.THIRD_PARTY. Methods register(), start(), update(), stop(), align() all raise an exception with message matching "not supported for third-party scorers". The scorer's name attribute is "ResponseMatch". The source is AssessmentSource(source_type=AssessmentSourceType.CODE, source_id="google_adk/ResponseMatch").
Signature: ResponseMatch(threshold=0.5)

Type: Function
Name: get_scorer
Location: mlflow/genai/scorers/google_adk/__init__.py
Signature: get_scorer(metric_name: str, **kwargs) -> ToolTrajectory | ResponseMatch
Description: Factory function that returns a scorer instance for the given metric name. Accepts "ToolTrajectory" and "ResponseMatch" as valid metric_name values, passing **kwargs to the corresponding class constructor. Raises an exception with a message matching "Unknown Google ADK metric" for unrecognized names.

Type: Function
Name: map_scorer_inputs_to_invocation
Location: mlflow/genai/scorers/google_adk/utils.py (must also be imported and accessible at mlflow/genai/scorers/google_adk/__init__.py level)
Signature: map_scorer_inputs_to_invocation(inputs=None, outputs=None, expectations=None, trace=None) -> tuple
Description: Converts scorer inputs into a (actual_invocation, expected_invocation) pair compatible with the ADK evaluator interface. The actual invocation has user_content.parts[0].text set to the inputs string and final_response.parts[0].text set to the outputs string. When a trace is provided (and expectations does not override with "actual_tool_calls"), actual.intermediate_data is populated with tool_uses extracted from trace TOOL spans. The expected invocation has intermediate_data populated from expectations["expected_tool_calls"] and final_response from expectations["expected_response"] (falling back to expectations["context"] or expectations["reference"]). When expectations is None or absent, expected.intermediate_data is None and expected.final_response is None. This function must be patchable at the mlflow.genai.scorers.google_adk module level (i.e., imported into __init__.py).

Type: Function
Name: _extract_actual_tool_calls
Location: mlflow/genai/scorers/google_adk/utils.py
Signature: _extract_actual_tool_calls(expectations, trace) -> list
Description: Extracts the list of actual tool calls as dicts with "name" and "args" keys. If expectations contains an "actual_tool_calls" key, returns that list (overriding trace extraction). Otherwise, extracts tool calls from spans of type SpanType.TOOL in the trace (using span name and span inputs as args). Returns an empty list if both expectations and trace are None or empty.

Type: Function
Name: check_adk_installed
Location: mlflow/genai/scorers/google_adk/__init__.py (may be defined in utils.py and imported here)
Signature: check_adk_installed() -> None
Description: Checks whether the google-adk package is installed and raises an error if not. Called at scorer initialization time. Must be patchable at the mlflow.genai.scorers.google_adk module level for testing without the package installed.

Type: Function
Name: _create_trajectory_evaluator
Location: mlflow/genai/scorers/google_adk/__init__.py
Signature: _create_trajectory_evaluator(threshold: float, match_type: str) -> object
Description: Creates and returns a Google ADK trajectory evaluator configured with the given threshold and match_type. Called by ToolTrajectory when scoring (called as _create_trajectory_evaluator(threshold, match_type) with positional arguments). Must be patchable at the mlflow.genai.scorers.google_adk module level for testing.

Type: Function
Name: _create_rouge_evaluator
Location: mlflow/genai/scorers/google_adk/__init__.py
Signature: _create_rouge_evaluator(threshold: float) -> object
Description: Creates and returns a Google ADK ROUGE-based text evaluator configured with the given threshold. Called by ResponseMatch when scoring. Must be patchable at the mlflow.genai.scorers.google_adk module level for testing.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.