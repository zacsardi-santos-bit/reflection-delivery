I'm working on a tensor comparison debug utility and need a few improvements to its logging and exit-code behavior.

*   A new module at sglang/srt/debug_utils/comparator/log_sink.py must define a LogSink class and a module-level log_sink singleton. LogSink must have an add(item) method accepting ErrorLog or InfoLog, and a context() context manager that captures items in a list instead of printing them.

*   When an ErrorLog is added to LogSink outside a context in JSON output mode, the emitted JSON must have an 'errors' field containing that item. When an InfoLog is added, the emitted JSON must have an 'infos' field containing that item and 'errors' must be empty.

*   The output_types module must define ErrorLog(category: str, message: str) and InfoLog(category: str, message: str) classes to replace the old GeneralWarning class.

*   The output_types module must define LogRecord(errors: list[ErrorLog], infos: list[InfoLog]) as an AnyRecord variant that replaces WarningRecord. It must survive a JSON round-trip via parse_record_json, preserving the type of each item.

*   LogRecord must have a to_text() method. In its output, ErrorLog entries must be rendered with a '✗' prefix and InfoLog entries must be rendered with a 'ℹ' prefix.

*   The output_types module must expose a _split_logs(items) function that accepts a mixed list of ErrorLog and InfoLog objects and returns a tuple of (list[ErrorLog], list[InfoLog]).

*   TensorComparisonRecord must replace its 'warnings' field with an 'errors' field (list[ErrorLog]) and add an 'infos' field (list[InfoLog]). A record with diff.passed=True but a non-empty errors list must have category=='failed'.

*   SkipComparisonRecord must replace its 'warnings' field with an 'errors' field (list[ErrorLog]). A record with a non-empty errors list must have category=='failed'.

*   NonTensorComparisonRecord must replace its 'warnings' field with an 'errors' field (list[ErrorLog]). A record with values_equal=True but a non-empty errors list must have category=='failed'.

*   A compute_exit_code function must be added to sglang/srt/debug_utils/comparator/utils.py with signature: compute_exit_code(summary: SummaryRecord, allow_skipped_pattern: str, skipped_names: list[str], allow_failed_pattern: str | None, failed_names: list[str]) -> int. It must return 1 if summary.passed == 0 (regardless of patterns). It must return 1 if any skipped name does not fully match allow_skipped_pattern. It must return 1 if allow_failed_pattern is None and there are failures, or if any failed name does not fully match allow_failed_pattern. Otherwise it returns 0.

*   The comparator CLI argument --allow-skip-pattern must be renamed to --allow-skipped-pattern. A new --allow-failed-pattern argument must be added. When allow-failed-pattern matches all failed tensor names and at least one tensor passed, the run() function must return exit code 0.

*   The comparator entrypoint module must no longer export _compute_exit_code. Exit code logic must use compute_exit_code from the utils module.

*   Internal modules that imported warning_sink from sglang.srt.debug_utils.comparator.warning_sink must be updated to import log_sink from sglang.srt.debug_utils.comparator.log_sink. Specifically: bundle_comparator and aux_loader must use log_sink.

*   The apply_dim_names function in sglang/srt/debug_utils/comparator/dims.py must raise a ValueError when the number of tensor dimensions does not match the number of names provided. The error message must match the pattern: 'dims metadata mismatch.*<N> dims.*shape \[...\].*<M> names \[...\].*fix the dims string'.

*   When TP replicated tensors have different shapes, the comparison record must have category=='failed' and the failed replicated_checks must have diff=None.


*   Interface details: Type: Class
Name: ErrorLog
Location: sglang/srt/debug_utils/comparator/output_types.py
Description: Replaces GeneralWarning. Represents an error-level log entry with category and message fields.
Signature: ErrorLog(category: str, message: str)

Type: Class
Name: InfoLog
Location: sglang/srt/debug_utils/comparator/output_types.py
Description: New informational log entry type with category and message fields.
Signature: InfoLog(category: str, message: str)

Type: Class
Name: LogRecord
Location: sglang/srt/debug_utils/comparator/output_types.py
Description: Replaces WarningRecord. Contains separate errors and infos lists. Must be a recognized AnyRecord variant for parse_record_json. The to_text() method renders errors prefixed with ✗ and infos prefixed with ℹ.
Signature:
  - Constructor: LogRecord(errors: list[ErrorLog], infos: list[InfoLog] = [])
  - to_text() -> str

Type: Function
Name: _split_logs
Location: sglang/srt/debug_utils/comparator/output_types.py
Description: Partitions a mixed list of ErrorLog and InfoLog objects into two separate lists.
Signature: _split_logs(items: list) -> tuple[list[ErrorLog], list[InfoLog]]

Type: Class
Name: LogSink
Location: sglang/srt/debug_utils/comparator/log_sink.py
Description: Replaces WarningSink. Collects ErrorLog and InfoLog entries. context() is a context manager; items added inside a context are captured in a list rather than printed. Items added outside a context are emitted to output (text or JSON). In JSON mode, ErrorLog items appear under "errors" and InfoLog items appear under "infos" in the output LogRecord.
Signature:
  - add(item: ErrorLog | InfoLog) -> None
  - context() -> ContextManager[list]

Type: Variable
Name: log_sink
Location: sglang/srt/debug_utils/comparator/log_sink.py
Description: Module-level singleton instance of LogSink. Replaces the old warning_sink singleton. Used by internal modules to emit log entries.

Type: Function
Name: compute_exit_code
Location: sglang/srt/debug_utils/comparator/utils.py
Description: Determines the process exit code from a comparison summary. Returns 0 if at least one tensor passed AND all skipped names match allow_skipped_pattern AND all failed names match allow_failed_pattern (if provided). Returns 1 if passed==0, if any skipped name does not match allow_skipped_pattern, or if any failed name does not match allow_failed_pattern (or allow_failed_pattern is None and failures exist).
Signature: compute_exit_code(summary: SummaryRecord, allow_skipped_pattern: str, skipped_names: list[str], allow_failed_pattern: str | None, failed_names: list[str]) -> int


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.