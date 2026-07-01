Migrate the existing GitLab API interaction to use the official GitLab Python client library. Implement a new module to manage API client creation and update existing functions to utilize structured job objects with attribute access instead of plain dictionaries.

*   Create a new module at `tasks/libs/ciproviders/gitlab_api.py`.
    *   Export the functions `generate_gitlab_full_configuration`, `read_includes`, and `get_gitlab_api`.
*   Implement `get_gitlab_api` in `tasks/libs/ciproviders/gitlab_api.py` to return a configured GitLab API client using the official python-gitlab library.
    *   Ensure the client supports `.projects.get(project_name)`, `.pipelines.get(pipeline_id).jobs.list()`, and `.jobs.get(job_id).trace()`.
*   Ensure `generate_gitlab_full_configuration` and `read_includes` are functional when imported from the new module.
    *   Maintain their existing behavior for resolving YAML includes, supporting `!reference` tags, and handling context variable overrides.
*   Update `FailedJobs.add_failed_job()` in `tasks/libs/types/types.py` to accept `ProjectJob` objects and access job properties via attribute access.
    *   Use attributes like `job.failure_type`, `job.allow_failure`, `job.web_url`, etc.
*   Modify the notification and statistics pipeline code:
    *   Access job objects using attributes consistent with `ProjectJob` objects.
    *   Replace dictionary key access (e.g., `job['url']`) with attribute access (e.g., `job.web_url`).
*   Update `send_message`, `send_stats`, and `check_consistent_failures` in `tasks/notify.py` to retrieve pipeline jobs and job traces using `get_gitlab_api`.
    *   Use the pattern `api.projects.get(...).pipelines.get(...).jobs.list()` and `api.projects.get(...).jobs.get(...).trace()`.
*   Ensure `update_statistics` accesses job names via `job.name` when computing failing jobs.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.