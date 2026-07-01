Refactor the Google quantum engine client to use a project ID for authentication and billing instead of an API key. Implement a request builder to include the project ID as a request header. Update the client to support optional custom discovery URLs and ensure environment-based configuration for project IDs.

*   Update the `Engine` class:
    *   Accept a required `project_id` parameter and store it as an instance attribute.
    *   Replace the `api_key` parameter with `project_id`.
    *   Use `discovery.build` with a `requestBuilder` keyword argument that includes the project ID in the request headers.
    *   Accept an optional `discovery_url` parameter; pass it as `discoveryServiceUrl` if provided.
    *   Raise a `ValueError` with 'both specified' if both `version` and `discovery_url` are supplied.
    *   Implement the `list_processors()` method to use `project_id` for constructing the API call path.
    *   Implement the `get_latest_calibration(processor_id)` method to construct the full path using `project_id`.
    *   Implement the `implied_job_config(job_config)` method to infer the project from `project_id`.

*   Update the `JobConfig` class:
    *   Remove `project_id` from the constructor.
    *   Ensure `JobConfig()` and `JobConfig(gcs_prefix=...)` are valid.
    *   Implement `__repr__` to return a string formatted as 'cirq.google.JobConfig(...)' with all fields.
    *   Implement value equality to ensure two instances with identical fields compare equal.

*   Implement the `engine_from_environment()` function:
    *   Read the `CIRQ_QUANTUM_ENGINE_DEFAULT_PROJECT_ID` environment variable.
    *   Use the environment variable's value as `project_id` when constructing the `Engine`.
    *   Raise `EnvironmentError` with a message containing 'CIRQ_QUANTUM_ENGINE_DEFAULT_PROJECT_ID' if the variable is not set.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.