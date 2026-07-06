I'm working with Apache Airflow and Amazon SageMaker Unified Studio.

*   SageMakerUnifiedStudioNotebookHook must extend AwsBaseHook and default client_type to 'datazone'. It must pop an optional endpoint_url keyword argument from kwargs before calling super().__init__().

*   SageMakerUnifiedStudioNotebookHook._validate_api_availability must raise RuntimeError with a message matching 'start_notebook_run.*not available' (regex) if start_notebook_run is missing from the client, and raise RuntimeError matching 'get_notebook_run.*not available' (regex) if get_notebook_run is missing. Both start_notebook_run and get_notebook_run must call _validate_api_availability before proceeding; calls against a client that lacks these methods must raise RuntimeError matching 'not available'.

*   SageMakerUnifiedStudioNotebookHook.start_notebook_run must call the underlying DataZone client using keyword arguments: domainIdentifier, owningProjectIdentifier, notebookIdentifier, and clientToken. When client_token is not provided, a UUID4 string must be auto-generated (36 characters, 4 hyphens, format 8-4-4-4-12). The following optional keyword arguments must be omitted from the client call entirely when their corresponding parameters are None or falsy: parameters (from notebook_parameters), computeConfiguration (from compute_configuration), timeoutConfiguration (from timeout_configuration). When workflow_name is provided, triggerSource={'type': 'WORKFLOW', 'name': workflow_name} must be included; when not provided, triggerSource must be absent.

*   SageMakerUnifiedStudioNotebookHook.get_notebook_run must call the DataZone client with keyword arguments domainIdentifier and identifier (where identifier is the notebook_run_id).

*   SageMakerUnifiedStudioNotebookHook._handle_status must return None for statuses QUEUED, STARTING, RUNNING, and STOPPING; return {'Status': 'SUCCEEDED', 'NotebookRunId': notebook_run_id} for SUCCEEDED; raise RuntimeError for STOPPED (with the status embedded in the message or using a fallback); raise RuntimeError containing the provided error_message when FAILED and error_message is non-empty; raise RuntimeError matching 'Exiting notebook run {notebook_run_id}. Status: FAILED' when FAILED with an empty error_message; raise RuntimeError matching 'Exiting notebook run {notebook_run_id}. Status: {status}' for any unexpected status value.

*   SageMakerUnifiedStudioNotebookHook.wait_for_notebook_run must poll get_notebook_run on each iteration and sleep waiter_delay seconds between polls. Max attempts must be derived using math.ceil(run_timeout * 60 / waiter_delay), where run_timeout = timeout_configuration['runTimeoutInMinutes'] when timeout_configuration is non-empty and contains that key, or TWELVE_HOURS_IN_MINUTES otherwise. With timeout_configuration={'runTimeoutInMinutes': 1} and waiter_delay=5, exactly 12 poll attempts must be made before raising RuntimeError matching 'Execution timed out'.

*   SageMakerUnifiedStudioNotebookHook.get_project_s3_path must return a string of the form 'amazon-sagemaker-{account_id}-{region}-{project_id}', using the hook's account_id and conn_region_name properties.

*   SageMakerUnifiedStudioNotebookHook.get_notebook_outputs must use S3Hook to read a JSON file at the S3 key 'sys/notebooks/{notebook_identifier}/runs/{notebook_run_id}/notebook_outputs.json' from the bucket returned by get_project_s3_path(owning_project_identifier). It must return {} when: S3 raises a ClientError with error code 'NoSuchKey'; S3 raises a ClientError with error code '404'; the file content is not valid JSON; the parsed JSON value is not a dict; the parsed dict is empty; or any other unexpected exception occurs.

*   SageMakerUnifiedStudioNotebookOperator must store the following constructor parameters as instance attributes with these defaults: notebook_identifier (required), domain_identifier (required), owning_project_identifier (required), client_token=None, notebook_parameters=None, compute_configuration=None, timeout_configuration=None, wait_for_completion=True, waiter_delay=10, deferrable=False.

*   SageMakerUnifiedStudioNotebookOperator.hook must return a SageMakerUnifiedStudioNotebookHook with client_type='datazone'.

*   SageMakerUnifiedStudioNotebookOperator.execute must: (1) call SageMakerUnifiedStudioLink.persist() to push a 'sagemaker_unified_studio' XCom entry — this happens unconditionally at the start of execute(); (2) call hook.start_notebook_run with all stored parameters plus workflow_name=context['dag'].dag_id; (3) extract notebook_run_id from the start response using key 'id' (not 'notebookRunId'); (4) when deferrable=True, defer to SageMakerUnifiedStudioNotebookTrigger with method_name='execute_complete' and not call wait_for_notebook_run — deferrable mode takes precedence over wait_for_completion=False; (5) when wait_for_completion=False and not deferrable, return {'notebook_run_id': notebook_run_id} immediately without calling wait or get_notebook_outputs; (6) when wait_for_completion=True and not deferrable, call hook.wait_for_notebook_run and then hook.get_notebook_outputs, push notebook_run_id to XCom, push each output as 'NOTEBOOK_OUTPUT.{key}' to XCom, and return {'notebook_run_id': ..., 'NOTEBOOK_OUTPUT.key': value, ...}. When there are no outputs, the total xcom_push call count from execute() must be 2 (persist link + notebook_run_id). When there are N outputs, the count must be 2 + N.

*   SageMakerUnifiedStudioNotebookOperator.execute_complete must raise AirflowException matching 'event is None' when event is None. When event['status'] is not 'success', raise RuntimeError matching 'Notebook run did not succeed'. On success, extract notebook_run_id from event['notebook_run_id'], call hook.get_notebook_outputs, push notebook_run_id to XCom as key 'notebook_run_id', push each output as 'NOTEBOOK_OUTPUT.{key}' to XCom. execute_complete must NOT call persist() (no extra sagemaker_unified_studio xcom push). Return {'notebook_run_id': ..., 'NOTEBOOK_OUTPUT.key': value, ...} or {'notebook_run_id': ...} if no outputs.

*   SageMakerUnifiedStudioNotebookSensor must store domain_identifier, owning_project_identifier, notebook_run_id, notebook_identifier, and endpoint_url (default None) as instance attributes. The hook property must return a SageMakerUnifiedStudioNotebookHook with client_type='datazone'.

*   SageMakerUnifiedStudioNotebookSensor.poke must call hook.get_notebook_run(notebook_run_id, domain_identifier=...) and return True for SUCCEEDED, False for QUEUED/STARTING/RUNNING/STOPPING, and raise RuntimeError matching 'Exiting notebook run {notebook_run_id}. State: {status}' for any other status (including FAILED, STOPPED, unknown values, and missing/empty status strings).

*   SageMakerUnifiedStudioNotebookSensor.execute must invoke the base sensor logic (poke), then call hook.get_notebook_outputs(notebook_identifier, notebook_run_id, owning_project_identifier), and push each output key-value pair to XCom as 'NOTEBOOK_OUTPUT.{key}'. When there are no outputs, xcom_push must not be called.

*   SageMakerUnifiedStudioNotebookTrigger must extend AwsBaseWaiterTrigger and compute waiter_max_attempts = math.ceil(run_timeout * 60 / waiter_delay) in __init__, where run_timeout comes from timeout_configuration['runTimeoutInMinutes'] when available and non-empty, otherwise TWELVE_HOURS_IN_MINUTES. The 'attempts' attribute (set by AwsBaseWaiterTrigger from waiter_max_attempts) must equal int(TWELVE_HOURS_IN_MINUTES * 60 / waiter_delay) with default timeout, and int(timeout_minutes * 60 / waiter_delay) with a custom timeout. waiter_name must be 'notebook_run_complete'. waiter_args must be {'domainIdentifier': domain_identifier, 'identifier': notebook_run_id}. return_key must be 'notebook_run_id'. return_value must be the notebook_run_id string. serialize() must return the classpath 'airflow.providers.amazon.aws.triggers.sagemaker_unified_studio_notebook.SageMakerUnifiedStudioNotebookTrigger' and kwargs including notebook_run_id, domain_identifier, owning_project_identifier, waiter_delay, timeout_configuration, and waiter_max_attempts. The hook() method must return SageMakerUnifiedStudioNotebookHook with client_type='datazone'. The module must export TWELVE_HOURS_IN_MINUTES = 720.

*   A custom boto waiter definition file must exist at providers/amazon/src/airflow/providers/amazon/aws/waiters/datazone.json. It must define a waiter named 'notebook_run_complete' that targets the GetNotebookRun operation and accepts SUCCEEDED as a success state and STOPPED/FAILED as failure states.


*   Interface details: ## Hook

Type: Class
Name: SageMakerUnifiedStudioNotebookHook
Location: providers/amazon/src/airflow/providers/amazon/aws/hooks/sagemaker_unified_studio_notebook.py
Description: AWS hook for the Amazon DataZone notebook-run APIs. Extends AwsBaseHook. When initialized, defaults client_type to "datazone".
Signature:
  __init__(*args, endpoint_url: str | None = None, **kwargs) — pops endpoint_url before passing remaining kwargs to AwsBaseHook; sets client_type="datazone" as default
  start_notebook_run(notebook_identifier: str, domain_identifier: str, owning_project_identifier: str, client_token: str | None = None, notebook_parameters: dict | None = None, compute_configuration: dict | None = None, timeout_configuration: dict | None = None, workflow_name: str | None = None) -> dict
  get_notebook_run(notebook_run_id: str, domain_identifier: str) -> dict
  _handle_status(notebook_run_id: str, status: str, error_message: str, waiter_delay: int = 10) -> dict | None
  wait_for_notebook_run(notebook_run_id: str, domain_identifier: str, waiter_delay: int = 10, timeout_configuration: dict | None = None) -> dict
  _validate_api_availability() -> None
  get_project_s3_path(project_id: str) -> str
  get_notebook_outputs(notebook_identifier: str, notebook_run_id: str, owning_project_identifier: str) -> dict

## Operator

Type: Class
Name: SageMakerUnifiedStudioNotebookOperator
Location: providers/amazon/src/airflow/providers/amazon/aws/operators/sagemaker_unified_studio_notebook.py
Description: Airflow operator that starts and optionally waits for a SageMaker Unified Studio notebook run. Extends AwsBaseOperator. Supports synchronous polling and deferrable (async) execution modes. Contains operator_extra_links that push a "sagemaker_unified_studio" XCom key via SageMakerUnifiedStudioLink.persist() inside execute().
Signature:
  __init__(*, notebook_identifier: str, domain_identifier: str, owning_project_identifier: str, client_token: str | None = None, notebook_parameters: dict | None = None, compute_configuration: dict | None = None, timeout_configuration: dict | None = None, wait_for_completion: bool = True, waiter_delay: int = 10, deferrable: bool = False, **kwargs)
  hook (property) -> SageMakerUnifiedStudioNotebookHook   # client_type="datazone"
  execute(context: dict) -> dict
  execute_complete(context: dict, event: dict | None = None) -> dict

## Sensor

Type: Class
Name: SageMakerUnifiedStudioNotebookSensor
Location: providers/amazon/src/airflow/providers/amazon/aws/sensors/sagemaker_unified_studio_notebook.py
Description: Airflow sensor that monitors a running SageMaker Unified Studio notebook run and reads its outputs once complete. Extends AwsBaseSensor.
Signature:
  __init__(*, domain_identifier: str, owning_project_identifier: str, notebook_run_id: str, notebook_identifier: str, endpoint_url: str | None = None, **kwargs)
  hook (property) -> SageMakerUnifiedStudioNotebookHook   # client_type="datazone"
  poke(context) -> bool
  execute(context) -> None

## Trigger

Type: Class
Name: SageMakerUnifiedStudioNotebookTrigger
Location: providers/amazon/src/airflow/providers/amazon/aws/triggers/sagemaker_unified_studio_notebook.py
Description: Deferrable trigger for a SageMaker Unified Studio notebook run. Extends AwsBaseWaiterTrigger. Uses a custom boto waiter named "notebook_run_complete".
Signature:
  __init__(notebook_run_id: str, domain_identifier: str, owning_project_identifier: str, waiter_delay: int = 10, timeout_configuration: dict | None = None, aws_conn_id: str | None = None, **kwargs)
  attempts (property/attribute) -> int     # stored from waiter_max_attempts computed in __init__
  waiter_name (property/attribute) -> str  # "notebook_run_complete"
  waiter_args (property/attribute) -> dict # {"domainIdentifier": ..., "identifier": ...}
  return_key (property/attribute) -> str   # "notebook_run_id"
  return_value (property/attribute) -> str # the notebook_run_id value
  serialize() -> tuple[str, dict]
  hook() -> SageMakerUnifiedStudioNotebookHook

## Module-level constant

Type: Constant
Name: TWELVE_HOURS_IN_MINUTES
Location: providers/amazon/src/airflow/providers/amazon/aws/triggers/sagemaker_unified_studio_notebook.py
Description: Numeric constant equal to 720 (12 * 60). Used as the default run timeout in minutes when no timeout_configuration is provided.

## Waiter definition

Type: JSON waiter file
Name: notebook_run_complete
Location: providers/amazon/src/airflow/providers/amazon/aws/waiters/datazone.json
Description: Custom boto waiter definition for polling GetNotebookRun. The waiter named "notebook_run_complete" polls the GetNotebookRun operation and accepts SUCCEEDED as success, STOPPED and FAILED as failure states.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.