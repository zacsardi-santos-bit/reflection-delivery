I'm working on our CI test runner script and need two improvements.

*   When a test's execution time exceeds the runtime threshold, the runner must prefix the log message with 'warn: ', producing output like 'warn: test/path/name.test took X.Xs' instead of the unprefixed form.

*   The runner must accept a '--stabilize-tests' command-line flag. When this flag is present and a test list is provided via '--test-list', the runner first performs an initial run of all tests, then reruns each test individually according to its group tag: tests tagged '[fast]' are rerun 9 times; tests tagged '[.][slow]' are rerun 2 times.

*   When '--changed-tests' is provided and the number of newly added tests (tests in the changed list but absent from the base list) is 500 or fewer, the runner must automatically stabilize those new tests after the initial run, performing 9 reruns per newly added fast test.

*   When '--changed-tests' is provided and the number of newly added tests exceeds 500, the runner must skip per-test reruns and instead perform exactly 2 additional runs of the entire set of new tests (3 total runs: 1 initial full run + 2 reruns of only the new tests).

*   When '--stabilize-tests' is active and any stabilization rerun returns a non-zero exit code, the overall runner must exit with return code 1 and must include the message 'error: stabilization rerun failure detected' in its stdout output.


*   Interface details: Type: Module
Name: run_tests
Location: scripts/ci/run_tests.py
Description: The CI test runner module. Tests import this module directly as `scripts.ci.run_tests`.

Type: Class
Name: ConfigRunResult
Location: scripts/ci/run_tests.py
Description: Named result object returned by a single config run. Must expose the following fields: returncode (int), passed_tests (int), failed_tests (int), skipped_tests (int), elapsed_seconds (float). Used when mocking the internal run_tests function.
Signature: ConfigRunResult(returncode: int, passed_tests: int, failed_tests: int, skipped_tests: int, elapsed_seconds: float)

Type: Function
Name: invoke
Location: scripts/ci/run_tests.py
Description: Entry point for running the test runner programmatically. Accepts CLI argument list and an optional working directory keyword argument.
Signature: invoke(cli_args: list[str], cwd: str | None = None) -> int

Type: CLI Flag
Name: --stabilize-tests
Location: scripts/ci/run_tests.py (argument parser)
Description: When present, triggers stabilization mode: after the initial run, each test in the list is rerun individually according to group policy (fast tests: 9 reruns; slow tests: 2 reruns). If any rerun fails, the overall exit code is 1 and stdout contains "error: stabilization rerun failure detected".

Type: CLI Flag
Name: --test-list
Location: scripts/ci/run_tests.py (argument parser)
Description: Path to a TSV file listing tests with name and group columns. Used by --stabilize-tests and --changed-tests.

Type: CLI Flag
Name: --changed-tests
Location: scripts/ci/run_tests.py (argument parser)
Description: Path to a file listing the tests present in the changed code. When provided, the runner computes newly added tests (in changed list but not in base --test-list) and automatically stabilizes them. If new test count <= 500: each new test is rerun 9 times individually. If new test count > 500: the whole new-test set is run 2 additional times (3 total runs).

Type: Internal function (mocked by tests)
Name: run_tests
Location: scripts/ci/run_tests.py
Description: The internal function that executes a single batch run. Tests mock this as `scripts.ci.run_tests.run_tests`. Must accept (config, batches, total_tests) and return a ConfigRunResult.
Signature: run_tests(config, batches: list, total_tests: int) -> ConfigRunResult


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.