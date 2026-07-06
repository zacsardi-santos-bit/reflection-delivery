I've been working on improving the DuckDB test infrastructure.

*   The run_tests module must expose a strip_ansi function that accepts a single string and returns the same string with all ANSI escape sequences removed.

*   The run_tests module must expose a format_signal_summary function that accepts an integer return code and returns a non-empty string describing the crash or signal corresponding to that code.

*   The summarize_failure_output function must accept an optional returncode keyword argument (defaulting to 0 or a neutral value). When this argument is a negative integer and no meaningful failure text is found in stderr, the function must identify the last test that was started-but-not-completed according to stdout progress lines, use it as the single reproduce target, and include format_signal_summary(returncode) as the diagnostic line.

*   When summarize_failure_output is called with a negative returncode and stderr contains sanitizer output (lines beginning with '==N==ERROR:'), the sanitizer text must be preferred over the signal summary. format_signal_summary output must not appear in the returned lines in this case.

*   The summarize_failure_output function must return a tuple where the first element of the lines list is a status/progress line (callers should slice from index 1 to obtain the meaningful failure lines). The returned lines may contain ANSI escape sequences.

*   Failure summaries produced by summarize_failure_output must use 'details: <message>' format (not 'expected: X, got Y; <message>'). When the stderr output includes 'Expected result:' and 'Actual result:' labeled sections, those sections must be included verbatim in the returned lines.

*   When rendering a failure from Catch2-style test output (containing a section header, assertion details, and expansion), summarize_failure_output must prefer the full assertion block including surrounding source lines over a compressed one-line summary.

*   When a batch failure in stdout contains progress-bar-style output (lines like '[N/M] (X%): <test>'), summarize_failure_output must parse this to identify the failing test rather than treating the entire stdout as unstructured text.

*   Skipped test summary output (the section beginning with 'Skipped tests for the following reasons:') must not appear inline within the per-failure block returned by summarize_failure_output. It must appear only in the aggregated summary section at the end of the overall run output.

*   The render_test_snippet function must strip any shared leading indentation from all returned snippet lines so that deeply-indented source code is displayed without unnecessary common whitespace prefix.

*   The test_runner_wrapper module's build_run_tests_argv function must return a list whose first element is the absolute path to the sibling binary (resolved from wrapper_path's parent directory joined with unittest_name), followed by all elements of forwarded_args.

*   The test_runner_wrapper module's main function must return 1 and print a message containing 'expected sibling unittest binary' to stderr when the sibling binary file does not exist.

*   The test_runner_wrapper module's main function must invoke run_tests.main with the argv produced by build_run_tests_argv, running from the source_root directory, and must restore the original working directory before returning. It must return the integer return code from run_tests.main.

*   CI workflow run: commands that invoke the unittest binary directly must use the build-output wrapper at a path matching the pattern build/<config>/test/run rather than invoking the Python helper script with an explicit binary path argument.


*   Interface details: Type: Function
Name: strip_ansi
Location: scripts/ci/run_tests.py
Signature: strip_ansi(text: str) -> str
Description: Strips ANSI escape sequences from the given string and returns the plain text.

Type: Function
Name: format_signal_summary
Location: scripts/ci/run_tests.py
Signature: format_signal_summary(returncode: int) -> str
Description: Returns a human-readable description of a crash or signal failure given a negative return code (e.g. -6 for SIGABRT, -11 for SIGSEGV).

Type: Function
Name: summarize_failure_output
Location: scripts/ci/run_tests.py
Signature: summarize_failure_output(message, stdout: str, stderr: str, batch: list[str], returncode: int = 0) -> tuple[list[str], list[str]]
Description: Parses test runner output and returns (lines, reproduce_batch). Now accepts an optional returncode parameter for signal/crash detection. The returned lines list begins with a status line at index 0 and may contain ANSI escape sequences. Failure summaries use "details: <msg>" format followed by "Expected result:" and "Actual result:" labeled sections. When returncode is negative and no meaningful failure output exists, the last-started test from stdout progress is used as the reproduce target and format_signal_summary(returncode) is appended as the diagnostic. Sanitizer output (lines matching "==N==ERROR: ...") in stderr takes priority over signal summary. Skipped test summary blocks (lines after "Skipped tests for the following reasons:") must not appear inline within the failure block; they belong only in the final summary.

Type: Function
Name: render_test_snippet
Location: scripts/ci/run_tests.py
Signature: render_test_snippet(path: str, line_no: int) -> list[str]
Description: Reads a source file and returns a formatted snippet centered on the given line number with line numbers and a ">" marker on the target line. The snippet strips any shared leading indentation across all lines so that deeply-indented code is displayed without unnecessary leading whitespace. Output lines may contain ANSI escape sequences; use strip_ansi to obtain plain text.

Type: Module
Name: test_runner_wrapper
Location: scripts/ci/test_runner_wrapper.py
Description: A self-contained wrapper module placed in the build output directory. Provided by the test patch; agents do not need to create it. Its public interface is used by tests in test_run_tests.py.

Type: Function
Name: build_run_tests_argv
Location: scripts/ci/test_runner_wrapper.py
Signature: build_run_tests_argv(forwarded_args: list[str], wrapper_path: str | Path, unittest_name: str = "unittest") -> list[str]
Description: Resolves the sibling unittest binary path (same directory as wrapper_path, named unittest_name) and returns a list with that absolute path as the first element followed by forwarded_args.

Type: Function
Name: main
Location: scripts/ci/test_runner_wrapper.py
Signature: main(forwarded_args: list[str] | None = None, *, wrapper_path: str | Path | None = None, source_root: str | Path | None = None, unittest_name: str = "unittest") -> int
Description: Entry point for the wrapper. If the sibling unittest binary does not exist as a file, prints "error: expected sibling unittest binary at <path>" to stderr and returns 1. Otherwise changes directory to source_root, calls run_tests.main() with the constructed argv, restores the original working directory, and returns run_tests.main()'s return code.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.