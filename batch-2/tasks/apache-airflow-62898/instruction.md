I'm working with Airflow's AI operator integrations and I need to add a human-in-the-loop approval step for generated outputs.

*   The LLMApprovalMixin class must provide a `defer_for_approval(context, generated_output, subject=None, body=None)` method. It must convert its `generated_output` argument to a string before use: Pydantic BaseModel instances must be serialized to compact JSON with no spaces after separators (e.g. '{"text":"Paris","confidence":0.95}'); all other non-string types must be converted using str().

*   The `defer_for_approval` method must call `upsert_hitl_detail` with the following keyword arguments: `ti_id` equal to the task instance ID from `context['task_instance'].id`; `options` equal to `['Approve', 'Reject']`; `subject` using the caller-supplied value or a default of 'Review output for task `{task_id}`' where `task_id` is the operator's task_id; `body` using the caller-supplied value or a default that includes both the operator's prompt and the generated output string; and `params` as described in the next requirement.

*   When `allow_modifications` is False, `defer_for_approval` must pass `params={}` to `upsert_hitl_detail`. When `allow_modifications` is True, `params` must contain an `'output'` key whose value is a dict with `'value'` set to the serialized output string and `'schema'` set to `{'type': 'string'}`.

*   When `approval_timeout` is set on the operator, `defer_for_approval` must pass `timeout_datetime = utcnow() + approval_timeout` to the HITLTrigger constructor. When `approval_timeout` is None, `timeout_datetime` must be None. The `self.defer` call must use `timeout=approval_timeout` (None when not set).

*   The `defer_for_approval` method must call `self.defer` with `method_name='execute_complete'` and `kwargs={'generated_output': <serialized_string_output>}`.

*   The `execute_complete(context, generated_output, event)` method must return `generated_output` when `event['chosen_options']` contains 'Approve' and no reviewer modification is applicable.

*   When `allow_modifications` is True and the event contains `params_input` with an `'output'` key whose value differs from `generated_output`, `execute_complete` must return `params_input['output']`. When `params_input` is None or does not contain an `'output'` key, `execute_complete` must return `generated_output`.

*   The `execute_complete` method must raise `HITLRejectException` with a message matching 'Output was rejected by the reviewer {responded_by_user}.' when `chosen_options` does not contain 'Approve' (including when `chosen_options` is an empty list). The `responded_by_user` value must be read from the event dict.

*   The `execute_complete` method must raise `HITLTriggerEventError` (with the event's error information accessible via str() of the exception) when the event contains an `'error'` key with `error_type` other than `'timeout'`. It must raise `HITLTimeoutError` when `error_type` is `'timeout'`, with a message that includes BOTH the literal text 'Approval timed out' AND the value from `event['error']` — for example the format 'Approval timed out: {event["error"]}' satisfies this requirement.

*   Both `LLMOperator` and `LLMSQLQueryOperator` must be subclasses of `LLMApprovalMixin`. Both must accept and store `require_approval: bool = False`, `allow_modifications: bool = False`, and `approval_timeout: timedelta | None = None` as constructor parameters with those default values.

*   When `require_approval=True`, `LLMOperator.execute()` must call `defer_for_approval` with the LLM-generated output instead of returning it, causing the task to defer (raise TaskDeferred) with `method_name='execute_complete'` and `kwargs['generated_output']` equal to the serialized output. When `require_approval=False`, `execute()` must return the output directly as before.

*   When `require_approval=True`, `LLMSQLQueryOperator.execute()` must validate the generated SQL for safety before calling `defer_for_approval`. If the SQL is unsafe, it must raise `SQLSafetyError` with a message containing 'not allowed' and must NOT call `upsert_hitl_detail`. Markdown code fences (e.g. '```sql\nSELECT 1\n```') must be stripped from the LLM output before deferring, so only the bare SQL is stored in `generated_output`.

*   `LLMSQLQueryOperator` must override `execute_complete` to re-validate the output when the reviewer modifies it: if the returned output differs from `generated_output` and fails SQL safety validation, `execute_complete` must raise `SQLSafetyError` with a message containing 'not allowed'. The timeout error handling (raising HITLTimeoutError with 'Approval timed out' in the message) is provided by the inherited LLMApprovalMixin implementation and does not need to be separately overridden in LLMSQLQueryOperator.


*   Interface details: Type: Class
Name: LLMApprovalMixin
Location: providers/common/ai/src/airflow/providers/common/ai/mixins/approval.py
Description: Mixin class that provides human-in-the-loop approval capability. Must be mixed into operator classes that have `prompt` (str), `task_id` (str), `approval_timeout` (timedelta | None), `allow_modifications` (bool), `defer` (callable), and `log` attributes. Provides two methods:
  defer_for_approval(context: dict, generated_output: Any, subject: str | None = None, body: str | None = None) -> None
  execute_complete(context: dict, generated_output: str, event: dict) -> Any

The `execute_complete` method raises HITLTimeoutError with a message that includes both the text 'Approval timed out' and the event's error value when error_type is 'timeout' (e.g. format: 'Approval timed out: {event["error"]}'). This ensures the message satisfies both the pattern 'Approval timed out' (checked by SQL operator tests) and the event's error content (checked by base mixin tests).

Type: Class
Name: LLMOperator
Location: providers/common/ai/src/airflow/providers/common/ai/operators/llm.py
Description: Must be a subclass of LLMApprovalMixin (in addition to its existing base class). Must accept and store the following new constructor parameters with these exact names and defaults:
  require_approval: bool = False
  allow_modifications: bool = False
  approval_timeout: timedelta | None = None
  When require_approval=True, execute(context) must call defer_for_approval rather than returning the output directly.

Type: Class
Name: LLMSQLQueryOperator
Location: providers/common/ai/src/airflow/providers/common/ai/operators/llm_sql.py
Description: Must be a subclass of LLMApprovalMixin (inherited via LLMOperator). Must accept and store the same approval parameters as LLMOperator (require_approval, allow_modifications, approval_timeout with the same defaults). When require_approval=True, execute(context) must validate the SQL before deferring (raising SQLSafetyError with message containing 'not allowed' if unsafe, without calling upsert_hitl_detail), strip markdown code fences from the output, then call defer_for_approval. Must override execute_complete(context, generated_output, event) to call super().execute_complete() for all error/rejection/approval logic, then re-validate the returned output if it differs from generated_output (raising SQLSafetyError with 'not allowed' if unsafe). The timeout error handling ('Approval timed out' message) is inherited from LLMApprovalMixin and does not need a separate override.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.