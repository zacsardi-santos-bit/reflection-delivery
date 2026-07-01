Implement support for SageMaker AutoML v2 job APIs in the AWS mock library to enable offline testing of these endpoints. Extend the mock to handle creating, describing, listing, stopping AutoML jobs, and managing tags.

*   Update `moto/sagemaker/responses.py`:
    *   Add methods to `SageMakerResponse`:
        *   `create_auto_ml_job_v2(self) -> str`
        *   `describe_auto_ml_job_v2(self) -> str`
        *   `list_auto_ml_jobs(self) -> str`
        *   `stop_auto_ml_job(self) -> str`

*   Update `moto/sagemaker/models.py`:
    *   Create a class `AutoMLJob` with an `arn` attribute.
    *   Add methods to `SageMakerModelBackend`:
        *   `create_auto_ml_job_v2(self, auto_ml_job_name, auto_ml_job_input_data_config, output_data_config, auto_ml_problem_type_config, role_arn, tags, security_config, auto_ml_job_objective, model_deploy_config, data_split_config) -> str`
        *   `describe_auto_ml_job_v2(self, auto_ml_job_name: str) -> dict`
        *   `list_auto_ml_jobs(self, creation_time_after, creation_time_before, last_modified_time_after, last_modified_time_before, name_contains, status_equals, sort_order, sort_by) -> list`
        *   `stop_auto_ml_job(self, auto_ml_job_name: str) -> None`
    *   Maintain a dictionary `self.auto_ml_jobs` for AutoML jobs and register it in the backend's ARN lookup table.

*   Update `moto/resourcegroupstaggingapi/models.py`:
    *   Include `self.sagemaker_backend.auto_ml_jobs` in resource iteration logic for `sagemaker` or `sagemaker:automl-job` resource type filters in `get_resources`.

*   Ensure the following behaviors:
    *   `CreateAutoMLJobV2` returns an ARN in the format `arn:aws:sagemaker:{region}:{account_id}:automl-job/{job_name}`.
    *   `DescribeAutoMLJobV2` includes all creation fields, default values for unspecified fields, and additional fields like `CreationTime`, `EndTime`, `LastModifiedTime`, `AutoMLJobStatus`, and `AutoMLJobSecondaryStatus`.
    *   `ListAutoMLJobs` supports filtering by name, status, creation time, last modified time, and sorting by name, status, or creation time.
    *   `StopAutoMLJob` changes job status to 'Stopped'.
    *   Tags are managed via `ListTags`, `AddTags`, and `DeleteTags` using job ARNs.
    *   Tagged AutoML jobs appear in `Resource Groups Tagging API` queries with a `sagemaker` resource type filter.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.