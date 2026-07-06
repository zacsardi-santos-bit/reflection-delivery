I'm working on a script to automatically detect CI performance regressions in our GitHub Actions workflows and send Slack alerts when jobs slow down significantly compared to their historical baseline.

*   The gh_api function must force the HTTP method to GET (by passing --method GET to the gh CLI subprocess) regardless of any other flags, so that workflow-run list endpoints return data instead of a 404.

*   The gh_api function must accept an optional 'event' keyword argument and, when it is non-empty, forward it to the API call. When event is an empty string, it must not be forwarded at all.

*   The parse_iso function must accept an ISO 8601 timestamp string with either a 'Z' suffix or a '+00:00' offset and return a datetime object with correct field values (year, hour, etc.). It must return None for an empty string, None input, or any unparseable value.

*   The duration_seconds function must accept two ISO 8601 timestamp strings (start, end) and return the elapsed time in whole seconds as an integer. It must return None if either string is unparseable, and must return None if the computed duration would be negative (i.e. end is before start).

*   The median function must accept a list of numbers and return the median value. For an odd-length list it returns the middle element; for an even-length list it returns the average of the two middle elements.

*   The format_duration function must accept a number of seconds and return a human-readable string. If the value is at least 60 seconds it must render as 'Xm Ys' where the seconds component is zero-padded to two digits (e.g., '1m 05s'). If the value is under 60 seconds it must render as 'Xs' (e.g., '45s').

*   The detect_regression function must accept latest_values (list), baseline_values (list), rel_threshold (float), and min_abs_increase_seconds (int). It returns a dict with at least 'latest' and 'rel_increase' keys when BOTH the relative increase exceeds rel_threshold AND the absolute increase exceeds min_abs_increase_seconds. It must return None when either threshold is not met, and must return None when either input list is empty. It must use the median of each list for comparison so that a single outlier in the baseline does not mask a real regression.

*   The get_recent_runs function must accept repo (str), workflow_file (str), branch (str), max_runs (int), only_successful (bool), and event (str). It must return a list of run dicts each containing at least 'id' and 'duration' (in seconds). When only_successful=True it must exclude runs whose conclusion is not 'success'; when False it must include failed runs. Runs with near-zero wall-clock durations (non-representative runs such as cancelled or instantly-skipped runs) must be dropped regardless of the only_successful flag. When the underlying API call returns None or fails, the function must return an empty list.

*   The get_run_jobs function must accept repo (str) and run_id (int/str) and return a dict mapping job name (str) to duration in seconds (int) for jobs whose conclusion is 'success'. Jobs with any other conclusion (e.g. 'skipped') must be omitted. If the subprocess command fails (non-zero return code), the function must return an empty dict.

*   The analyze_jobs function must accept repo (str), latest_runs (list), baseline_runs (list), min_baseline_runs (int), rel_threshold (float), and min_abs_increase_seconds (int). It must return a list of regression dicts each containing at least a 'job' key. Jobs that appear in latest runs but not in enough baseline runs (fewer than min_baseline_runs samples) must be skipped. Jobs that do not meet the regression thresholds must not appear in the output.

*   The format_slack_message function must accept repo (str), workflow (str), branch (str), overall_regression (dict), job_regressions (list of dicts), recent_runs (list), rel_threshold (float), and channel (str). It must return a dict with a 'channel' key equal to the provided channel, a 'blocks' list containing at least one entry with type 'header', and a 'text' field that includes the branch name. Job names from job_regressions must appear somewhere in the serialized message.


*   Interface details: Type: Function
Name: gh_api
Location: scripts/ci/analyze_ci_job_durations.py
Signature: gh_api(endpoint: str, **kwargs) -> str | None
Description: Calls the GitHub CLI 'gh api' command as a subprocess. Must include '--method GET' in the command arguments. Accepts keyword arguments such as 'branch' and 'event' that are forwarded to the API call. Returns the stdout string on success or None on failure.

Type: Function
Name: parse_iso
Location: scripts/ci/analyze_ci_job_durations.py
Signature: parse_iso(s: str | None) -> datetime | None
Description: Parses an ISO 8601 timestamp string (with 'Z' suffix or '+00:00' offset) and returns a datetime object. Returns None for empty string, None input, or any unparseable value.

Type: Function
Name: duration_seconds
Location: scripts/ci/analyze_ci_job_durations.py
Signature: duration_seconds(start: str, end: str) -> int | None
Description: Computes the elapsed time in whole seconds between two ISO 8601 timestamp strings. Returns None if either string is unparseable or if the result would be negative.

Type: Function
Name: median
Location: scripts/ci/analyze_ci_job_durations.py
Signature: median(values: list) -> float | int
Description: Computes the median of a list of numbers. Returns the middle element for odd-length lists; returns the average of the two middle elements for even-length lists.

Type: Function
Name: format_duration
Location: scripts/ci/analyze_ci_job_durations.py
Signature: format_duration(seconds: int | float) -> str
Description: Formats a duration in seconds as a human-readable string. Returns 'Xm Ys' (seconds zero-padded to 2 digits) when at least 60 seconds, or 'Xs' when under 60 seconds.

Type: Function
Name: detect_regression
Location: scripts/ci/analyze_ci_job_durations.py
Signature: detect_regression(latest_values: list, baseline_values: list, rel_threshold: float, min_abs_increase_seconds: int) -> dict | None
Description: Compares latest duration values against baseline values using their medians. Returns a dict with at least keys 'latest' (median of latest_values), 'baseline' (median of baseline_values), 'increase' (absolute increase in seconds), and 'rel_increase' (relative increase as a float, e.g. 0.5 for 50%) when BOTH the relative increase exceeds rel_threshold AND the absolute increase exceeds min_abs_increase_seconds. Returns None otherwise, and returns None when either input list is empty.

Type: Function
Name: get_recent_runs
Location: scripts/ci/analyze_ci_job_durations.py
Signature: get_recent_runs(repo: str, workflow_file: str, branch: str, max_runs: int, only_successful: bool, event: str) -> list[dict]
Description: Fetches recent GitHub Actions workflow runs for the given repo and workflow file. Returns a list of run dicts, each containing at least 'id' and 'duration' (in seconds). When only_successful=True, excludes runs with a non-'success' conclusion. Drops runs with near-zero durations regardless of the only_successful flag. When event is a non-empty string, passes it as a keyword argument named 'event' to gh_api; when event is empty, does not pass the event argument at all. Returns [] when gh_api returns None or fails.

Type: Function
Name: get_run_jobs
Location: scripts/ci/analyze_ci_job_durations.py
Signature: get_run_jobs(repo: str, run_id: int) -> dict[str, int]
Description: Fetches the individual jobs for a specific workflow run. Returns a dict mapping job name (str) to duration in seconds (int) for jobs whose conclusion is 'success'. Skips jobs with any other conclusion. Returns an empty dict if the subprocess command exits with a non-zero return code.

Type: Function
Name: analyze_jobs
Location: scripts/ci/analyze_ci_job_durations.py
Signature: analyze_jobs(repo: str, latest_runs: list, baseline_runs: list, min_baseline_runs: int, rel_threshold: float, min_abs_increase_seconds: int) -> list[dict]
Description: Analyzes per-job durations across latest and baseline runs. Returns a list of regression dicts, each containing at least a 'job' key (job name) plus the regression details (latest, baseline, increase, rel_increase). Skips jobs that do not appear in at least min_baseline_runs baseline runs. Only includes jobs where detect_regression returns a non-None result.

Type: Function
Name: format_slack_message
Location: scripts/ci/analyze_ci_job_durations.py
Signature: format_slack_message(repo: str, workflow: str, branch: str, overall_regression: dict, job_regressions: list, recent_runs: list, rel_threshold: float, channel: str) -> dict
Description: Formats a Slack message dict for a CI regression alert. The returned dict must have: 'channel' equal to the provided channel argument; 'blocks' as a list containing at least one dict with 'type' equal to 'header'; and 'text' as a string that includes the branch name. Job names from job_regressions must appear somewhere in the serialized message content.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.