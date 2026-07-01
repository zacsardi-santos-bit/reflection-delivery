Implement a security check for AWS CodeBuild projects to detect sensitive credentials stored as plaintext environment variables. Ensure that variables stored securely via a parameter store are not flagged, and allow users to specify an exclusion list for non-sensitive variables.

Requirements:

*   Implement the `codebuild_project_no_secrets_in_variables` class in `prowler/providers/aws/services/codebuild/codebuild_project_no_secrets_in_variables/codebuild_project_no_secrets_in_variables.py`.
    *   Include an `__init__.py` file in the same directory.
    *   Expose an `execute()` method that:
        *   Iterates over all CodeBuild projects from `codebuild_client.projects`.
        *   Returns a list of findings, one per project.
        *   Returns an empty list if there are no CodeBuild projects.
        *   Sets `region`, `resource_id`, and `resource_arn` for each finding.
        *   Sets status to 'PASS' if no sensitive plaintext credentials are found, with `status_extended` as: 'CodeBuild project {project_name} does not have sensitive environment plaintext credentials.'
        *   Sets status to 'FAIL' if sensitive plaintext credentials are found, with `status_extended` as: 'CodeBuild project {project_name} has sensitive environment plaintext credentials in variables: {secret_type} in variable {var_name}[, {secret_type} in variable {var_name}].'
        *   Uses `detect_secrets` library to identify secret types in variable values.
*   Only scan environment variables of type 'PLAINTEXT'.
*   Skip scanning for variables listed in `codebuild_client.audit_config['excluded_sensitive_environment_variables']`.
*   Update the `Project` model in `prowler/providers/aws/services/codebuild/codebuild_service.py`:
    *   Add an optional `environment_variables` field of type `Optional[List[EnvironmentVariable]]`.
*   Define an `EnvironmentVariable` Pydantic model in `prowler/providers/aws/services/codebuild/codebuild_service.py` with fields:
    *   `name`: string
    *   `value`: string
    *   `type`: string
*   Update `prowler/config/config.yaml` to include `excluded_sensitive_environment_variables` as an empty list under the `aws` section.
*   Ensure `tests/config/fixtures/config.yaml` and `tests/config/config_test.py` reflect the same configuration key and default value.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.