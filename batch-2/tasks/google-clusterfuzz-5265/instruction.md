I need to implement a new daily cron job for the ClusterFuzz project that aggregates fuzzer statistics from the previous day and loads them into a cloud data warehouse.

*   The aggregate_fuzzer_stats module must expose a main(args) function that accepts a list of command-line argument strings (e.g., ['--non-dry-run'] or ['--non-dry-run', '--date', '2026-04-30']).

*   When called with '--non-dry-run', main() must create a BigQuery dataset with datasetId 'fuzzer_stats' in the current GCP project, passing the projectId and a datasetReference body with both projectId and datasetId set.

*   main() must create a BigQuery table named 'daily_stats' in dataset 'fuzzer_stats' with time partitioning of type 'DAY' on the 'date' field, and with the schema defined by the module-level constant DAILY_STATS_SCHEMA.

*   main() must query an existing data source using the SQL pattern: DATE(TIMESTAMP_SECONDS(CAST(timestamp AS INT64))) = 'YYYY-MM-DD', where YYYY-MM-DD is the target date (yesterday by default).

*   main() must create a BigQuery load job targeting dataset 'fuzzer_stats', with writeDisposition 'WRITE_TRUNCATE', and a destinationTable tableId of 'daily_stats$YYYYMMDD' (where YYYYMMDD is the target date in compact format).

*   The data uploaded to BigQuery via the load job must be newline-delimited JSON, where each record contains the fields: fuzzer_name, date, testcases_executed, testcase_execution_duration, testcases_generated, testcase_generation_duration, and fuzzing_duration — populated from the query results.

*   The module must upload data using MediaIoBaseUpload (importable from the module's own namespace as clusterfuzz._internal.cron.aggregate_fuzzer_stats.MediaIoBaseUpload).

*   If the dataset insert API call returns an HTTP 409 (already exists) response, main() must silently ignore the error and continue execution, still attempting table creation and data upload.

*   If the dataset insert API call returns any non-409 HTTP error (e.g., 500 Internal Server Error), main() must re-raise the HttpError.

*   When called with '--date YYYY-MM-DD', main() must use the provided date instead of yesterday for both the query filter and the load job destination table ID (e.g., 'daily_stats$20260430' for date '2026-04-30').

*   When called with '--date invalid-date' (a value that does not match the YYYY-MM-DD date format), main() must raise SystemExit (i.e., argument parsing must fail with a non-zero exit).

*   The module must define a module-level constant DAILY_STATS_SCHEMA used as the BigQuery table schema.


*   Interface details: Type: Function
Name: main
Location: src/clusterfuzz/_internal/cron/aggregate_fuzzer_stats.py
Signature: main(args: list[str]) -> None
Description: Entry point for the aggregate_fuzzer_stats cron job. Accepts a list of CLI argument strings. Supported arguments include '--non-dry-run' (required to execute) and '--date YYYY-MM-DD' (optional override for target date; defaults to yesterday). Raises SystemExit if '--date' is given with an invalid date format. Raises HttpError for non-409 HTTP errors during BigQuery dataset creation. Creates BigQuery dataset 'fuzzer_stats' and table 'daily_stats' (ignoring 409 Already Exists), queries fuzzer stats for the target date, and loads results via a BigQuery load job with writeDisposition 'WRITE_TRUNCATE' into a daily partition named 'daily_stats$YYYYMMDD'.

Type: Constant
Name: DAILY_STATS_SCHEMA
Location: src/clusterfuzz/_internal/cron/aggregate_fuzzer_stats.py
Description: Module-level constant defining the BigQuery table schema for the 'daily_stats' table. Used as the 'schema' field when creating the table.

Type: Import
Name: MediaIoBaseUpload
Location: src/clusterfuzz/_internal/cron/aggregate_fuzzer_stats.py
Description: Must be imported into the aggregate_fuzzer_stats module's own namespace so it can be patched at 'clusterfuzz._internal.cron.aggregate_fuzzer_stats.MediaIoBaseUpload'. Used to upload the JSON-encoded fuzzer stats as the body of the BigQuery load job.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.