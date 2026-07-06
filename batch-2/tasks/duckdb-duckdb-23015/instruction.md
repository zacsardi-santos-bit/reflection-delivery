Our CI test runner's failure output is really hard to read.

*   The `summarize_failure_output` function must accept four arguments in order: an optional timeout/message string (or None), a stdout string, a stderr string, and a list of test file path strings representing the batch. It must return a tuple of two lists of strings: the formatted output lines and the reproduce batch (the list of test file paths to rerun).

*   When the message argument begins with 'batch timed out after N seconds', the function must produce a lines list containing exactly one entry: `"error: timeout (Ns) for <test_path>."` where N is extracted from the message and formatted as an integer when it is a whole number. The reproduce batch must contain only the identified timed-out test.

*   For timeout failures, the timed-out test is identified as follows: if stdout contains progress lines in the format `[N/M] (P%): <path>` with some tests showing completion via a 'took' suffix and others not, use the first test in the batch list that was not completed. If no progress information is available, use the last test in the batch list.

*   When stderr contains a structured test failure block (a numbered header like `N. <test_path>:<line_number>` followed by separator lines), the function must extract the failing test path and line number from that block, and set the reproduce batch to contain only that test path.

*   For 'Wrong result in query!' failures in stderr, the lines output must include: `"error: FAIL <test_path>"`, an empty string, a mismatch summary formatted as `"expected: <expected_value>, got <actual_value>; <mismatch_description>"`, an empty string, and then the snippet lines from `render_test_snippet` for that test and line number. The expected and actual values are extracted from the `<actual> <> <expected>` pattern in the stderr block, and the mismatch description comes from the 'Mismatch on row ...' line.

*   For generic structured errors in stderr (non-wrong-result), the lines output must include `"error: FAIL <test_path>"`, an empty string, and the content of the structured stderr block up to (but not including) any catch/unittest trailer lines starting with `~~~`.

*   When stderr is absent or contains no structured block, but stdout contains a fatal signal failure pattern (a 'FAILED:' header followed by a fatal error condition and signal), the function must identify the last test mentioned in progress start lines (lines matching `[N/M] (P%): <path>` without a 'took' suffix) as the failing test, and produce lines: `"error: FAIL <test_path>"`, an empty string, and the signal name (e.g., `"SIGSEGV - Segmentation violation signal"`).

*   When stdout contains a 'FAILED:' block followed by 'explicitly with message:', the lines output must include `"error: FAIL <test_path>"`, an empty string, and the explicit message text.

*   The `render_test_snippet` function must accept a file path string and a 1-indexed line number integer. It must read the file and return a list of formatted strings. The target line must be formatted as `"  > N  <content>"` and all other lines as `"    N  <content>"` where N is the 1-indexed line number padded to consistent width. Blank lines at the start and end of the returned list must be trimmed. The snippet window expands backward from the target line to the nearest preceding blank line and forward to the nearest following blank line.

*   When a failed test batch is retried, the runner output must include `"retrying failed test <test_name> (attempt X/Y, retry Z/W)"` where `<test_name>` is the specific failing test file path (not a batch index), and `"error: FAIL <test_name>"` must appear in the output for that failure.

*   When a previously failed test passes on a retry attempt, the runner output must include `"recovered: passed on retry X/Y"` where X/Y reflects the retry count and configuration limit.

*   When all retries for a test batch are exhausted, the final failure summary must use the compact format: `"error: FAIL <test_name>"` followed by mismatch details from the last failed attempt. The output must not contain `"### failed test batch"` headers or `"attempts:"` sections. Failure details must not be repeated for each retry attempt — only the final attempt's details are shown in the summary.

*   For timeout retries, the output must include `"error: timeout (Ns) for <test_name>."` and `"retrying failed test <test_name>"` naming the specific test.


*   Interface details: Type: Function
Name: summarize_failure_output
Location: scripts/ci/run_tests.py
Signature: summarize_failure_output(message: str | None, stdout: str, stderr: str, batch: list) -> tuple[list[str], list[str]]
Description: Parses CI test batch failure information and returns a tuple of (lines, reproduce_batch). The `lines` list contains human-readable formatted output lines describing the failure. The `reproduce_batch` list contains the test file path(s) that should be rerun to reproduce the failure. The `message` argument is a timeout/error message string or None; `stdout` and `stderr` are process outputs; `batch` is the list of test file paths in the failing batch.

Type: Function
Name: render_test_snippet
Location: scripts/ci/run_tests.py
Signature: render_test_snippet(test_name: str, line_number: int) -> list[str]
Description: Reads a test file and returns a formatted list of lines centered on the given 1-indexed line number. The target line is prefixed with `"  > N  "` and surrounding lines with `"    N  "` where N is the line number. The snippet window expands backward to the nearest preceding blank line and forward to the nearest following blank line. Blank lines at the beginning and end of the returned list are trimmed.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.