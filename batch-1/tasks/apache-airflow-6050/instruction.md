Implement a context manager for the Google Cloud base hook to manage credential setup, allowing precise scoping of credential availability. Update the Kubernetes Engine pod operator to utilize this new context manager, removing redundant credential setup logic.

*   Add a context manager method named `provide_gcp_credential_file_as_context` to the `GoogleCloudBaseHook` class in `airflow/contrib/hooks/gcp_api_base_hook.py`.
    *   If the connection has a `key_path` extra, set the `GOOGLE_APPLICATION_CREDENTIALS` environment variable to this key path during the context.
    *   If the connection has a `keyfile_dict` extra (JSON string), write the JSON content to a temporary file and set `GOOGLE_APPLICATION_CREDENTIALS` to the temporary file's path during the context.
    *   Ensure the environment variable is restored to its original state after the context exits, whether normally or due to an exception.
    *   Remove the environment variable on exit if it was not set before entering the context.

*   Update the `GKEPodOperator` in `airflow/gcp/operators/kubernetes_engine.py`.
    *   Modify the `execute(self, context)` method to use the `provide_gcp_credential_file_as_context` context manager from `GoogleCloudBaseHook`.
    *   Ensure `GOOGLE_APPLICATION_CREDENTIALS` points to a temporary file when the connection specifies a `keyfile_dict` during subprocess execution.
    *   Remove the private helper methods `_set_env_from_extras` and `_get_field` from `GKEPodOperator`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.