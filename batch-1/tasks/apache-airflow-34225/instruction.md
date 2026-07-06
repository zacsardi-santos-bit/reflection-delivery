Implement functionality to automatically generate monitoring links for EMR Serverless jobs in Airflow. These links should direct users to relevant AWS console pages based on the job's logging configuration. Ensure the links are context-aware and require minimal user configuration.

*   Add new link classes in `airflow/providers/amazon/aws/links/emr.py`:
    *   `EmrServerlessLogsLink`: Extends `BaseAwsLink`. Constructs a URL to Spark Driver stdout logs. Use `format_link(application_id, job_run_id, **kwargs)` to retrieve and modify the dashboard URL.
    *   `EmrServerlessDashboardLink`: Extends `BaseAwsLink`. Constructs a URL to the EMR Serverless application dashboard. Use `format_link(application_id, job_run_id, **kwargs)` to retrieve the dashboard URL unchanged.
    *   `EmrServerlessS3LogsLink`: Extends `BaseAwsLink`. Constructs a URL to the S3 console for job logs. Use `format_link(**kwargs)` to parse `log_uri` and construct the S3 console URL.
    *   `EmrServerlessCloudWatchLogsLink`: Extends `BaseAwsLink`. Constructs a CloudWatch console deep link. Use `format_link(**kwargs)` to URL-encode `awslogs_group` and construct the CloudWatch URL.

*   Implement `get_serverless_dashboard_url` in `airflow/providers/amazon/aws/links/emr.py`:
    *   Accept keyword-only parameters: `aws_conn_id`, `emr_serverless_client`, `application_id`, `job_run_id`.
    *   Raise `AirflowException` with "Requires either an AWS connection ID or an EMR Serverless Client" if neither or both `aws_conn_id` and `emr_serverless_client` are provided.
    *   Use `EmrServerlessHook` with `aws_conn_id` and single retry configuration if `aws_conn_id` is provided.
    *   Use `emr_serverless_client` directly if provided.
    *   Call `get_dashboard_for_job_run(applicationId=application_id, jobRunId=job_run_id)` and return `urlparse(response["url"])` if "url" is in the response.

*   Update `EmrServerlessStartJobOperator` in `airflow/providers/amazon/aws/operators/emr.py`:
    *   Accept a new boolean parameter `enable_application_ui_links` defaulting to `False`.
    *   In `execute(context)`, after job run starts, call `persist()` on link classes based on `configuration_overrides` and `enable_application_ui_links`:
        *   If `s3MonitoringConfiguration` is present, call `EmrServerlessS3LogsLink.persist()` with relevant parameters.
        *   If `cloudWatchLoggingConfiguration` is enabled, call `EmrServerlessCloudWatchLogsLink.persist()` with relevant parameters.
        *   If `enable_application_ui_links` is `True`, call `EmrServerlessDashboardLink.persist()` and, if job driver contains "sparkSubmit", also call `EmrServerlessLogsLink.persist()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.