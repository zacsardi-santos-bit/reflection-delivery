Implement the integration for the Anomali Security Analytics Alerts platform to enable analysts to create search jobs, retrieve search job results, and update alerts. Ensure the integration supports the specified functionalities and adheres to the given requirements.

*   Implement the `Client` class:
    *   Accept `server_url`, `username`, `api_key`, `verify`, and `proxy` as constructor parameters.
    *   Use these parameters to facilitate communication with the Anomali Security Analytics API.
    *   Locate this class in `Packs/AnomaliSecurityAnalyticsAlerts/Integrations/AnomaliSecurityAnalyticsAlerts/AnomaliSecurityAnalyticsAlerts.py`.

*   Implement the `command_create_search_job` function:
    *   Accept a `client` and an `args` dictionary with keys: 'query', 'source', 'from', and 'to'.
    *   Return a `CommandResults` object with outputs including the 'job_id' from the server response.
    *   Ensure the `readable_output` contains the string 'Search Job Created'.
    *   Locate this function in `Packs/AnomaliSecurityAnalyticsAlerts/Integrations/AnomaliSecurityAnalyticsAlerts/AnomaliSecurityAnalyticsAlerts.py`.

*   Implement the `command_get_search_job_results` function:
    *   Accept a `client` and an `args` dictionary with keys: 'job_id', 'offset', and 'fetch_size'.
    *   Always return a list containing exactly one `CommandResults` object.
    *   If job status is not 'DONE', include 'job_id' and 'status' in outputs; `readable_output` should indicate the job is still running.
    *   If the response contains an 'error', `readable_output` must include 'No results found for Job ID: {job_id}', 'Error message: {error_value}', and 'Please verify the Job ID and try again.'
    *   If the job is DONE and results contain 'fields' and 'records', include 'job_id' and 'records' (as a list of dicts) in outputs; `readable_output` should contain 'Search Job Results' and field names as table headers.
    *   If the job is DONE but lacks 'fields' or 'records', set outputs to the raw results dict; `readable_output` should contain 'Search Job Results' and the raw data.
    *   Locate this function in `Packs/AnomaliSecurityAnalyticsAlerts/Integrations/AnomaliSecurityAnalyticsAlerts/AnomaliSecurityAnalyticsAlerts.py`.

*   Implement the `command_update_alert` function:
    *   Accept a `client` and an `args` dictionary with 'uuid' (required), 'status' (optional), and 'comment' (optional).
    *   Return a `CommandResults` object with `readable_output` containing 'Update Alert' if 'status' or 'comment' is provided.
    *   Raise a `DemistoException` with the message "Please provide either 'status' or 'comment' parameter." if neither is present.
    *   Locate this function in `Packs/AnomaliSecurityAnalyticsAlerts/Integrations/AnomaliSecurityAnalyticsAlerts/AnomaliSecurityAnalyticsAlerts.py`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.