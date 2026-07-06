I need to add two CI utility scripts to our project to help track and report on flaky end-to-end browser tests.

*   The escape_slack_mrkdwn function must replace '&' with '&amp;', '<' with '&lt;', and '>' with '&gt;'. Text without any of these characters must be returned unchanged.

*   The identify_flaky_candidates function must return an empty list when the failures dictionary is empty or when the total run count is zero.

*   The identify_flaky_candidates function must return a list of candidate dicts each containing at least a 'test' key (string matching the input dict key) and a 'failure_rate' key (numeric value greater than zero). Tests with a very low failure rate relative to total runs must be excluded (e.g., 1 failure across 10 runs with 3 browsers is excluded).

*   The identify_flaky_candidates function must return results sorted by failure_rate in descending order.

*   The format_slack_message function must return a dict with a 'channel' key equal to 'airflow-3-ui', a 'text' key, and a 'blocks' key containing a list of block objects.

*   When format_slack_message is called with no flaky candidates, the blocks list must contain an entry whose string representation includes 'No flaky test candidates'.

*   When format_slack_message is called with no test failures, the blocks list must contain an entry whose string representation includes 'No test failures'.

*   When format_slack_message is called with failures, fixme tests, and candidates, the blocks must include the test name from candidates (e.g., 'test_a'), the fixme test name (e.g., 'test_b'), and the failure rate formatted as a percentage string (e.g., '50.0%').

*   When format_slack_message receives test names or error messages containing '<', '>', or '&', the blocks output must contain the escaped forms '&lt;', '&gt;', and '&amp;' respectively.

*   The truncate_error function must strip leading and trailing whitespace, replace newline characters with spaces, and return messages of 300 characters or fewer unchanged.

*   The truncate_error function must truncate messages longer than 300 characters to exactly 300 characters plus '...' (total length 303).

*   The extract_test_title function must return a string in the format '{file}: {part1} > {part2} > ...' by joining non-empty elements of titlePath with ' > '. If titlePath is empty or has no non-empty parts, only the file path is returned. If the location key is absent, 'unknown' is used in place of the file path.

*   The extract_failures function must recursively traverse nested suites, returning only failed/unexpected tests as a list of dicts each with 'test', 'status', 'error', and 'spec_file' keys. It must return an empty list for empty input.

*   The extract_fixme_tests function must recursively traverse nested suites, returning only tests annotated with type 'fixme' as a list of dicts each with 'test', 'status', and 'annotations' keys. It must return an empty list for empty input.

*   The main function in the extract script must read RESULTS_JSON, OUTPUT_DIR, BROWSER, and RUN_ID from environment variables, create the output directory, and write 'failures.json' and 'fixme_tests.json' to that directory. When the results file does not exist, failures.json must contain {"failures": [], "metadata": {"has_results": false}} and fixme_tests.json must contain {"fixme_tests": []}. When the results file exists and is valid, failures.json must contain the extracted failures list and metadata with 'browser' and 'has_results': true, and fixme_tests.json must contain the extracted fixme tests list.


*   Interface details: Type: Function
Name: escape_slack_mrkdwn
Location: scripts/ci/analyze_e2e_flaky_tests.py
Signature: escape_slack_mrkdwn(text: str) -> str
Description: Escapes special characters for Slack mrkdwn format. Replaces '&' with '&amp;', '<' with '&lt;', and '>' with '&gt;'.

Type: Function
Name: identify_flaky_candidates
Location: scripts/ci/analyze_e2e_flaky_tests.py
Signature: identify_flaky_candidates(failures_by_test: dict, runs_with_data: int) -> list
Description: Identifies tests that exceed a minimum failure-rate threshold and are candidates for being marked as needing a fix. Returns an empty list if runs_with_data is 0 or failures_by_test is empty. Each returned dict must include at least 'test' (str) and 'failure_rate' (float). Results are sorted by failure_rate descending.

Type: Function
Name: format_slack_message
Location: scripts/ci/analyze_e2e_flaky_tests.py
Signature: format_slack_message(failures_by_test: dict, fixme_tests: dict, flaky_candidates: list, runs: list, runs_with_data: int, repo: str) -> dict
Description: Formats the aggregated analysis as a Slack Block Kit message dict. The returned dict must have 'channel' set to 'airflow-3-ui', a 'text' key with a plain-text fallback, and a 'blocks' key with a list of block objects. Test names and errors must be escaped via escape_slack_mrkdwn.

Type: Function
Name: truncate_error
Location: scripts/ci/extract_e2e_test_results.py
Signature: truncate_error(message: str) -> str
Description: Normalizes an error message by stripping leading/trailing whitespace, replacing newline characters with spaces, and truncating to 300 characters (appending '...' if truncated, giving a total of 303 characters for long input).

Type: Function
Name: extract_test_title
Location: scripts/ci/extract_e2e_test_results.py
Signature: extract_test_title(spec: dict) -> str
Description: Builds the full test title from the spec dict. Uses spec['location']['file'] as the file path (defaults to 'unknown' if missing). Joins non-empty elements of spec['titlePath'] with ' > ' and returns '{file}: {joined_title}'. If there are no non-empty title parts, returns only the file path.

Type: Function
Name: extract_failures
Location: scripts/ci/extract_e2e_test_results.py
Signature: extract_failures(suites: list) -> list
Description: Recursively traverses Playwright JSON suites and returns a list of dicts for failed/unexpected tests. Each dict includes 'test' (str), 'status' (str), 'error' (str), and 'spec_file' (str). Returns an empty list for empty input.

Type: Function
Name: extract_fixme_tests
Location: scripts/ci/extract_e2e_test_results.py
Signature: extract_fixme_tests(suites: list) -> list
Description: Recursively traverses Playwright JSON suites and returns a list of dicts for tests annotated with type 'fixme'. Each dict includes 'test' (str), 'status' (str), and 'annotations' (list of dicts with 'type' and 'description' keys). Returns an empty list for empty input.

Type: Function
Name: main
Location: scripts/ci/extract_e2e_test_results.py
Signature: main() -> None
Description: Entry point for the extract script. Reads RESULTS_JSON, OUTPUT_DIR, BROWSER, and RUN_ID environment variables. Creates OUTPUT_DIR and writes 'failures.json' (with 'failures' list and 'metadata' dict including 'has_results' bool and 'browser' str) and 'fixme_tests.json' (with 'fixme_tests' list). When RESULTS_JSON path does not exist, writes empty lists with has_results=False.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.