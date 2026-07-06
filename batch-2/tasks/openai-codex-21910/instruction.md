I'm working with the Python SDK and the current API for controlling how permission escalations are handled during thread and turn operations requires setting two separate low-level parameters together.

*   ApprovalMode must be a string enum defined in sdk/python/src/openai_codex/api.py with exactly two members: deny_all (name='deny_all', value='deny_all') and auto_review (name='auto_review', value='auto_review'). It must be exported from the openai_codex root package and included in __all__.

*   The _approval_mode_settings function must map ApprovalMode.deny_all to internal params that serialize (with by_alias=True, exclude_none=True, mode='json') to {"approvalPolicy": "never"} (no approvalsReviewer key present), and must map ApprovalMode.auto_review to {"approvalPolicy": "on-request", "approvalsReviewer": "auto_review"}.

*   The _approval_mode_settings function must raise ValueError for any input that is not an ApprovalMode instance (e.g., a plain string like 'allow_all'). The error message must contain the string 'deny_all, auto_review' listing the valid mode values.

*   Codex.thread_start and AsyncCodex.thread_start must accept a keyword-only approval_mode parameter of type ApprovalMode with a default of ApprovalMode.auto_review. The two previous parameters approval_policy and approvals_reviewer must be removed from these method signatures.

*   Codex.thread_resume, Codex.thread_fork, Thread.turn, Thread.run, AsyncCodex.thread_resume, AsyncCodex.thread_fork, AsyncThread.turn, and AsyncThread.run must accept a keyword-only approval_mode parameter of type ApprovalMode | None with a default of None. The two previous parameters approval_policy and approvals_reviewer must be removed from these method signatures.

*   When approval_mode is None (the default for thread_resume, thread_fork, turn, and run), no approval-related keys (approvalPolicy, approvalsReviewer) are included in the serialized params sent to the app-server client. The serialized approval settings for that call must be an empty dict {}.

*   When approval_mode is explicitly set on turn or run, the approval settings are serialized and included in the params for that call only, without affecting other calls. Thread start approval settings do not propagate automatically to subsequent turn calls.


*   Interface details: Type: Class (Enum)
Name: ApprovalMode
Location: sdk/python/src/openai_codex/api.py
Description: High-level approval behavior for escalated permission requests. A str enum with exactly two members: deny_all (name="deny_all", value="deny_all") and auto_review (name="auto_review", value="auto_review"). Must be exported from the root openai_codex package and listed in __all__.

Type: Function
Name: _approval_mode_settings
Location: sdk/python/src/openai_codex/api.py
Signature: _approval_mode_settings(approval_mode: ApprovalMode) -> tuple[AskForApproval, ApprovalsReviewer | None]
Description: Maps a public ApprovalMode value to the generated app-server start params. For ApprovalMode.deny_all, returns params that serialize (with by_alias=True, exclude_none=True, mode="json") to {"approvalPolicy": "never"} with no approvalsReviewer key. For ApprovalMode.auto_review, returns params that serialize to {"approvalPolicy": "on-request", "approvalsReviewer": "auto_review"}. If the argument is not an ApprovalMode instance (e.g., a plain string), raises ValueError with a message that contains the string "deny_all, auto_review".

Type: Method
Name: thread_start
Location: sdk/python/src/openai_codex/api.py (on class Codex)
Signature: thread_start(self, *, approval_mode: ApprovalMode = ApprovalMode.auto_review, ...) -> Thread
Description: Replaces approval_policy and approvals_reviewer keyword parameters with a single approval_mode parameter. Default is ApprovalMode.auto_review.

Type: Method
Name: thread_resume
Location: sdk/python/src/openai_codex/api.py (on class Codex)
Signature: thread_resume(self, thread_id: str, *, approval_mode: ApprovalMode | None = None, ...) -> Thread
Description: Replaces approval_policy and approvals_reviewer keyword parameters with a single approval_mode parameter. Default is None (preserve existing settings, no override sent).

Type: Method
Name: thread_fork
Location: sdk/python/src/openai_codex/api.py (on class Codex)
Signature: thread_fork(self, thread_id: str, *, approval_mode: ApprovalMode | None = None, ...) -> Thread
Description: Replaces approval_policy and approvals_reviewer keyword parameters with a single approval_mode parameter. Default is None (preserve existing settings, no override sent).

Type: Method
Name: turn
Location: sdk/python/src/openai_codex/api.py (on class Thread)
Signature: turn(self, input: Input, *, approval_mode: ApprovalMode | None = None, ...) -> TurnHandle
Description: Replaces approval_policy and approvals_reviewer keyword parameters with a single approval_mode parameter. Default is None (preserve existing settings, no override sent).

Type: Method
Name: run
Location: sdk/python/src/openai_codex/api.py (on class Thread)
Signature: run(self, input: str | Input, *, approval_mode: ApprovalMode | None = None, ...) -> RunResult
Description: Replaces approval_policy and approvals_reviewer keyword parameters with a single approval_mode parameter. Default is None (preserve existing settings, no override sent).

Type: Method
Name: thread_start
Location: sdk/python/src/openai_codex/api.py (on class AsyncCodex)
Signature: thread_start(self, *, approval_mode: ApprovalMode = ApprovalMode.auto_review, ...) -> AsyncThread
Description: Async counterpart to Codex.thread_start. Replaces approval_policy and approvals_reviewer with approval_mode. Default is ApprovalMode.auto_review.

Type: Method
Name: thread_resume
Location: sdk/python/src/openai_codex/api.py (on class AsyncCodex)
Signature: thread_resume(self, thread_id: str, *, approval_mode: ApprovalMode | None = None, ...) -> AsyncThread
Description: Async counterpart to Codex.thread_resume. Default is None.

Type: Method
Name: thread_fork
Location: sdk/python/src/openai_codex/api.py (on class AsyncCodex)
Signature: thread_fork(self, thread_id: str, *, approval_mode: ApprovalMode | None = None, ...) -> AsyncThread
Description: Async counterpart to Codex.thread_fork. Default is None.

Type: Method
Name: turn
Location: sdk/python/src/openai_codex/api.py (on class AsyncThread)
Signature: turn(self, input: Input, *, approval_mode: ApprovalMode | None = None, ...) -> AsyncTurnHandle
Description: Async counterpart to Thread.turn. Default is None.

Type: Method
Name: run
Location: sdk/python/src/openai_codex/api.py (on class AsyncThread)
Signature: run(self, input: str | Input, *, approval_mode: ApprovalMode | None = None, ...) -> RunResult
Description: Async counterpart to Thread.run. Default is None.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.