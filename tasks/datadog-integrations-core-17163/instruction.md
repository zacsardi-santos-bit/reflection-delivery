Expand the CockroachDB integration to collect a comprehensive set of metrics for background jobs. Ensure these metrics are mapped correctly to facilitate automatic collection through the Prometheus monitoring endpoint.

*   Update `METRIC_MAP` and `OMV2_METRIC_MAP` in `cockroachdb/datadog_checks/cockroachdb/metrics.py`:
    *   Inspect `METRIC_MAP` to determine existing mappings.
    *   Add new job metrics to `OMV2_METRIC_MAP` only if they are not present in `METRIC_MAP`.
    *   Ensure no metric key appears in both maps, as enforced by `test_no_duplicate_metrics_in_maps`.

*   For `OMV2_METRIC_MAP`:
    *   Map Prometheus metric names to Datadog names without the '.count' suffix for counter-type metrics.
    *   Include the following changefeed job metrics:
        *   `jobs_changefeed_currently_idle` → `jobs.changefeed.currently_idle`
        *   `jobs_changefeed_currently_running` → `jobs.changefeed.currently_running`
        *   `jobs_changefeed_expired_pts_records` → `jobs.changefeed.expired_pts_records`
        *   `jobs_changefeed_fail_or_cancel_completed` → `jobs.changefeed.fail_or_cancel_completed`
        *   `jobs_changefeed_fail_or_cancel_failed` → `jobs.changefeed.fail_or_cancel_failed`
        *   `jobs_changefeed_fail_or_cancel_retry_error` → `jobs.changefeed.fail_or_cancel_retry_error`
        *   `jobs_changefeed_protected_record_count` → `jobs.changefeed.protected_record_count`
        *   `jobs_changefeed_resume_completed` → `jobs.changefeed.resume_completed`
        *   `jobs_changefeed_resume_failed` → `jobs.changefeed.resume_failed`

*   Ensure support for all metrics in the `JOBS_METRICS` set defined in `cockroachdb/tests/common.py`:
    *   Include job state metrics (idle, paused, running).
    *   Include completion and failure metrics (resume completed, failed, retry error).
    *   Include fail-or-cancel metrics and protected timestamp metrics across all job types.

*   Do not create or modify the fixture file `cockroachdb/tests/fixtures/jobs_metrics.txt` or any test patches.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.