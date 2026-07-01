Update the analytics tracking script to route traffic through Ghost's proxy endpoint and implement a local development mode for testing. Ensure the script reads configuration from data attributes and update the analytics reporting APIs with test fixtures to verify query results.

*   Modify the analytics tracking script:
    *   Use `analytics_events` for the `data-datasource` attribute, sourced from `tinybird.tracker.datasource`.
    *   Set `https://e.ghost.org/tb/web_analytics` as the default `data-host` endpoint, sourced from `tinybird.tracker.endpoint`.
    *   Render the script in the page head when `tinybird.tracker` configuration is present, without needing `tinybird.tracker.scriptUrl`.
    *   Accept `host` and `token` via `data-host` and `data-token` attributes. Remove separate `data-storage` attribute.
    *   Use `host` from `data-host` to construct the event submission URL.

*   Implement local development mode:
    *   Add a `local` sub-object in the configuration with fields: `enabled` (boolean), `endpoint` (string URL), `token` (string), and `datasource` (string).
    *   When `tinybird.tracker.local.enabled` is `true`, use `tinybird.tracker.local.endpoint` for `data-host` and `tinybird.tracker.local.token` for `data-token`.

*   Update analytics API:
    *   Ensure KPI queries return per-date rows with `date`, `visits`, `pageviews`, `bounce_rate`, and `avg_session_sec`.
        *   Multi-day ranges: `date` as `YYYY-MM-DD`.
        *   Single-day ranges: `date` as `YYYY-MM-DD HH:MM:SS` with 24 hourly rows.
    *   Ensure top-dimension queries return rows ordered by descending visit count with dimension fields and `visits`.
    *   Support optional filters: `browser`, `device`, `location`, `os`, `pathname`, `source`, `member_status`, `timezone`.
    *   Filter results based on `member_status` (`paid` or `undefined`).
    *   Adjust date boundaries according to the `timezone` parameter.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.