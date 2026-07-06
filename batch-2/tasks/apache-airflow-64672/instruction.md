I'm working with the Google Cloud Composer provider for Airflow and I've noticed it doesn't work correctly with Cloud Composer environments running newer versions of Airflow.

*   CloudComposerHook.trigger_dag_run must accept a new required integer parameter composer_airflow_version. When this value is less than 3, the method must call make_composer_airflow_api_request with a path of /api/v1/dags/{dag_id}/dagRuns. When the value is 3 or greater, it must use /api/v2/dags/{dag_id}/dagRuns.

*   CloudComposerHook.get_dag_runs must accept a new required integer parameter composer_airflow_version and select the REST API path prefix (/api/v1/ or /api/v2/) accordingly, using the same version threshold (< 3 maps to v1, >= 3 maps to v2).

*   CloudComposerHook.get_task_instances must accept a new required integer parameter composer_airflow_version and select the REST API path prefix (/api/v1/ or /api/v2/) accordingly for the path /api/v{x}/dags/{dag_id}/dagRuns/~/taskInstances, preserving any query string suffix.

*   CloudComposerHook must expose a static method get_airflow_rest_api_version(composer_airflow_version: int) -> str that returns the string 'v1' when composer_airflow_version is less than 3, and 'v2' when it is 3 or greater.

*   CloudComposerAsyncHook.get_dag_runs must accept a new required integer parameter composer_airflow_version and construct the request path using the same v1/v2 selection logic. Internally it must use get_sync_hook() and its get_airflow_rest_api_version method for version resolution.

*   CloudComposerAsyncHook.get_task_instances must accept a new required integer parameter composer_airflow_version and construct the versioned request path using the same logic.

*   CloudComposerDAGRunSensor must remove use_rest_api from its explicit constructor parameters. If use_rest_api is supplied as a keyword argument at instantiation, the constructor must emit an AirflowProviderDeprecationWarning (this applies whether the value is True or False).

*   CloudComposerDAGRunTrigger must remove use_rest_api from its explicit constructor parameters. If use_rest_api is supplied as a keyword argument at instantiation, the constructor must emit an AirflowProviderDeprecationWarning.

*   CloudComposerDAGRunTrigger.serialize() must return a dict of constructor arguments that does not include the key 'use_rest_api'.

*   CloudComposerTriggerDAGRunOperator.execute must extract the Airflow major version from the target Cloud Composer environment and pass it as composer_airflow_version (int) to hook.trigger_dag_run.


*   Interface details: Type: Method
Name: trigger_dag_run
Location: providers/google/src/airflow/providers/google/cloud/hooks/cloud_composer.py
Signature: trigger_dag_run(self, composer_airflow_uri: str, composer_dag_id: str, composer_dag_conf: dict, composer_airflow_version: int, timeout: float | None = None) -> Any
Description: Triggers a DAG run via the Airflow REST API. Accepts a composer_airflow_version int parameter to determine which API version to target. When composer_airflow_version < 3, uses path /api/v1/dags/{dag_id}/dagRuns; when >= 3, uses /api/v2/dags/{dag_id}/dagRuns.

Type: Method
Name: get_dag_runs
Location: providers/google/src/airflow/providers/google/cloud/hooks/cloud_composer.py
Signature: get_dag_runs(self, composer_airflow_uri: str, composer_dag_id: str, composer_airflow_version: int, timeout: float | None = None) -> Any
Description: Retrieves DAG runs via the Airflow REST API. Accepts composer_airflow_version to select the correct API path (/api/v1/ or /api/v2/).

Type: Method
Name: get_task_instances
Location: providers/google/src/airflow/providers/google/cloud/hooks/cloud_composer.py
Signature: get_task_instances(self, composer_airflow_uri: str, composer_dag_id: str, composer_airflow_version: int, query_parameters: dict | None = None, timeout: float | None = None) -> Any
Description: Retrieves task instances via the Airflow REST API. Accepts composer_airflow_version to select the correct API path. Path pattern: /api/v{1|2}/dags/{dag_id}/dagRuns/~/taskInstances{query_string}.

Type: Static Method
Name: get_airflow_rest_api_version
Location: providers/google/src/airflow/providers/google/cloud/hooks/cloud_composer.py
Signature: get_airflow_rest_api_version(composer_airflow_version: int) -> str
Description: Returns "v1" when composer_airflow_version is less than 3, and "v2" when composer_airflow_version is 3 or greater. Used by both CloudComposerHook and CloudComposerAsyncHook to determine the REST API version string to include in request paths.

Type: Class
Name: CloudComposerHook
Location: providers/google/src/airflow/providers/google/cloud/hooks/cloud_composer.py
Description: Synchronous hook for Google Cloud Composer. Must expose get_airflow_rest_api_version as a static/class method callable on both the class and its instances.

Type: Method
Name: get_dag_runs
Location: providers/google/src/airflow/providers/google/cloud/hooks/cloud_composer.py (CloudComposerAsyncHook)
Signature: async get_dag_runs(self, composer_airflow_uri: str, composer_dag_id: str, composer_airflow_version: int, timeout: float | None = None) -> Any
Description: Async version of get_dag_runs. Accepts composer_airflow_version int and constructs /api/v1/ or /api/v2/ path accordingly. Uses get_sync_hook() to access get_airflow_rest_api_version for version resolution.

Type: Method
Name: get_task_instances
Location: providers/google/src/airflow/providers/google/cloud/hooks/cloud_composer.py (CloudComposerAsyncHook)
Signature: async get_task_instances(self, composer_airflow_uri: str, composer_dag_id: str, composer_airflow_version: int, query_parameters: dict | None = None, timeout: float | None = None) -> Any
Description: Async version of get_task_instances. Accepts composer_airflow_version int and constructs the correct versioned API path. Uses get_sync_hook() for version resolution.

Type: Class
Name: CloudComposerDAGRunSensor
Location: providers/google/src/airflow/providers/google/cloud/sensors/cloud_composer.py
Description: Sensor that polls for DAG run completion. The use_rest_api parameter must be removed from the explicit constructor signature. When use_rest_api is passed as a keyword argument (via **kwargs), the constructor must emit an AirflowProviderDeprecationWarning. The sensor must function correctly without use_rest_api being set.

Type: Class
Name: CloudComposerDAGRunTrigger
Location: providers/google/src/airflow/providers/google/cloud/triggers/cloud_composer.py
Description: Trigger that polls for DAG run completion. The use_rest_api parameter must be removed from the explicit constructor signature. When use_rest_api is passed as a keyword argument (via **kwargs), the constructor must emit an AirflowProviderDeprecationWarning. The serialize() method must return a dict that does NOT include the "use_rest_api" key.

Type: Method
Name: serialize
Location: providers/google/src/airflow/providers/google/cloud/triggers/cloud_composer.py (CloudComposerDAGRunTrigger)
Signature: serialize(self) -> tuple[str, dict]
Description: Returns the trigger class path and a dict of its constructor arguments. The returned dict must NOT include "use_rest_api" as a key.

Type: Method
Name: execute
Location: providers/google/src/airflow/providers/google/cloud/operators/cloud_composer.py (CloudComposerTriggerDAGRunOperator)
Description: Executes the operator. Must determine the Airflow major version of the target Cloud Composer environment and pass it as composer_airflow_version (int) when calling hook.trigger_dag_run.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.