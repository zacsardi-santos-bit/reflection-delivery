Create a utility script in the `utils/` directory to automatically update GitHub pull request descriptions with CI run summaries. Ensure the script posts a status badge and a detailed recap of the CI results, updating these sections in-place on subsequent runs and cleaning up old-format comments.

*   Implement the module in `utils/update_pr_ci_dashboard_recap.py` with the following constants:
    *   `BADGE_START`, `BADGE_END`, `RECAP_START`, `RECAP_END`, `BADGE_URL`, `OLD_DASHBOARD_COMMENT_MARKERS`.

*   Implement the following functions:
    *   `prometheus_string(value) -> str`: Convert and escape values for Prometheus queries.
    *   `first_value(results) -> float | None`: Extract the first numeric value from Prometheus query results.
    *   `format_number(value) -> str`: Format numbers with thousands separators and two decimal places where necessary.
    *   `format_duration(seconds) -> str`: Format durations into human-readable strings.
    *   `render_ci_badge(pr_number, dashboard_url) -> str`: Create a CI status badge block.
    *   `render_ci_recap(dashboard_url, recap, workflow_run, quality_failed) -> str`: Create a CI recap block with metrics or fallback information.
    *   `replace_marked_block(body, start_marker, end_marker, replacement) -> str | None`: Replace marked text blocks within a string.
    *   `inject_ci_recap(body, recap_text) -> str`: Inject or replace the CI recap in the PR body.
    *   `inject_ci_badge(body, badge_text) -> str`: Inject or replace the CI badge in the PR body.
    *   `get_metric_value(query, fallback_query=None, fallback_on_zero=False) -> float | None`: Retrieve metric values from Prometheus.
    *   `get_latest_run_id(pr_number) -> str | None`: Get the latest CI run ID for a PR.
    *   `get_ci_recap(pr_number, run_url, run_conclusion) -> dict`: Generate a dictionary of CI metrics.
    *   `github_paginate(path, token, key=None) -> list`: Handle paginated GitHub API responses.
    *   `find_open_pr_for_sha(repo, token, sha) -> dict | None`: Find an open PR matching a specific SHA.
    *   `quality_job_failed(repo, token, run_id) -> bool`: Check if the code quality job failed.
    *   `delete_old_dashboard_comments(repo, token, pr_number) -> None`: Remove outdated dashboard comments from PRs.

*   Ensure the script:
    *   Updates the PR description with a status badge and a detailed recap.
    *   Handles missing metrics by using workflow run data.
    *   Detects and highlights code quality check failures.
    *   Cleans up old-format dashboard comments.
    *   Correctly processes paginated API responses and manages GitHub interactions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.