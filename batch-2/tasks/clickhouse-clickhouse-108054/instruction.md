I'm working on two small cleanup tasks in the ClickHouse repository.

*   The file ci/jobs/scripts/functional_tests/export_coverage.py must not contain the string '--max_rows_to_read_leaf' anywhere in its content.

*   The file tests/integration/test_refreshable_mat_view_replicated/test.py must contain a function named '_wait_batch_log_count' and must not contain any reference to '_wait_batch_log_max_t'.


*   Interface details: Type: Function
Name: _wait_batch_log_count
Location: tests/integration/test_refreshable_mat_view_replicated/test.py
Signature: _wait_batch_log_count(at_least, timeout=120)
Description: Replaces the old _wait_batch_log_max_t function. Polls the batch_log table until it has at least `at_least` rows (or until timeout), checking either node in the cluster. Returns the count when the threshold is reached, or raises AssertionError on timeout. The old name _wait_batch_log_max_t must not appear anywhere in the file.

Type: Constant
Name: LOGS_SAVER_CLIENT_OPTIONS
Location: ci/jobs/scripts/functional_tests/export_coverage.py
Description: A string constant inside the CoverageExporter class holding ClickHouse client flags used when exporting coverage logs. Must not contain the flag '--max_rows_to_read_leaf'.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.