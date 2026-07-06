I'm working on the LLM serving layer in our Ray-based system, and I'm running into a log flooding problem.

*   The `_is_fatal_engine_error(exc)` function must return True for any exception that is an instance of `EngineDeadError` (from vllm.v1.engine.exceptions), including subclasses (e.g. Ray-wrapped subclasses), and False for all other exception types such as ValueError or RuntimeError.

*   The `_FatalEngineErrorLogHandler` class must accept a `cooldown_s` (float) constructor parameter and initialize with internal state: `_first_logged=False`, `_suppressed_count=0`, `_last_summary_time=0.0`.

*   The `_FatalEngineErrorLogHandler.log(exc, request_id, status_code)` method must, for the first fatal engine error, log a full traceback via `logger.error(...)` with `exc_info=exc` as a keyword argument, set `_first_logged=True`, set `_last_summary_time` to `time.monotonic()`, and leave `_suppressed_count` at 0.

*   For subsequent fatal engine errors arriving within the cooldown window, `log()` must suppress the log and increment `_suppressed_count`. Only one full-traceback log is emitted per cooldown window.

*   When a fatal engine error arrives after the cooldown window has elapsed, `log()` must emit a summary via `logger.error(msg, count)` where the first positional argument contains the word 'Suppressed' and the second positional argument is the suppressed count, then reset `_suppressed_count` to 0 and update `_last_summary_time` to `time.monotonic()`.

*   When a fatal engine error arrives after 2× the cooldown duration has elapsed since `_last_summary_time` (a quiet period), `log()` must reset `_first_logged` to False and log a fresh full traceback with `exc_info=exc` instead of emitting a suppressed-count summary.

*   For non-fatal errors with status code 500, `log()` must call `logger.error(...)` on every invocation without any suppression, and must not affect `_first_logged`, `_suppressed_count`, or `_last_summary_time`.

*   For non-fatal errors with status code 4xx (e.g. 400), `log()` must call `logger.warning(...)` on every invocation without any suppression, and must not affect the fatal-error state attributes.

*   A module-level instance `_fatal_error_log_handler` of `_FatalEngineErrorLogHandler` must exist in `python/ray/llm/_internal/serve/utils/server_utils.py`.

*   The `get_response_for_error(exc, request_id)` function must call `_fatal_error_log_handler.log(exc, request_id, 500)` using positional arguments in that order, and must return an object whose `.error` attribute is not None and whose `.error.message` contains the value of `request_id`.


*   Interface details: Type: Function
Name: _is_fatal_engine_error
Location: python/ray/llm/_internal/serve/utils/server_utils.py
Signature: _is_fatal_engine_error(exc: Exception) -> bool
Description: Returns True if the exception is an instance of EngineDeadError (from vllm.v1.engine.exceptions) or any subclass thereof (including Ray-wrapped subclasses). Returns False for all other exception types.

Type: Class
Name: _FatalEngineErrorLogHandler
Location: python/ray/llm/_internal/serve/utils/server_utils.py
Description: A log rate limiter for fatal engine errors. Suppresses duplicate fatal-error logs within a cooldown window and emits a summary when the window expires. Resets to fresh-traceback mode after a quiet period.
Signature: __init__(self, cooldown_s: float) -> None
  Instance attributes (must be set in __init__):
    _first_logged: bool = False
    _suppressed_count: int = 0
    _last_summary_time: float = 0.0
  Method: log(self, exc: Exception, request_id: str, status_code: int) -> None

Type: Variable
Name: _fatal_error_log_handler
Location: python/ray/llm/_internal/serve/utils/server_utils.py
Description: Module-level instance of _FatalEngineErrorLogHandler. Must exist as a module-level attribute accessible via server_utils._fatal_error_log_handler.

Type: Function
Name: get_response_for_error
Location: python/ray/llm/_internal/serve/utils/server_utils.py
Signature: get_response_for_error(exc: Exception, request_id: str) -> ErrorResponse
Description: Builds and returns an error response for the given exception. Must call _fatal_error_log_handler.log(exc, request_id, 500) with those exact positional arguments, and return an object whose .error attribute is not None and whose .error.message contains the request_id string.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.