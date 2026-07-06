I'm working with the Azure Synapse Pipeline integration in Apache Airflow.

*   Must export AzureSynapsePipelineAsyncHook from the Azure Synapse hooks module (providers/microsoft/azure/src/airflow/providers/microsoft/azure/hooks/synapse.py) so it can be imported alongside AzureSynapsePipelineHook, AzureSynapsePipelineRunException, and AzureSynapsePipelineRunStatus.

*   AzureSynapsePipelineAsyncHook must accept azure_synapse_conn_id and azure_synapse_workspace_dev_endpoint as constructor parameters, and must maintain an instance attribute named _async_conn (initialized to None) that holds the async connection object.

*   The get_async_conn() async method, when the connection supplies login (client ID), password (client secret), and tenantId, must instantiate AsyncClientSecretCredential with keyword arguments client_id, client_secret, and tenant_id, then instantiate AsyncArtifactsClient with keyword arguments endpoint (the workspace dev endpoint) and credential, and return the resulting client.

*   The get_async_conn() async method, when the connection has no login or password, must instantiate AsyncDefaultAzureCredential with keyword arguments managed_identity_client_id and workload_identity_tenant_id, then instantiate AsyncArtifactsClient with keyword arguments endpoint and credential, and return the resulting client.

*   The get_async_conn() async method must raise ValueError with a message that contains the text 'Tenant ID' when login and password are provided but no tenant ID is configured in the connection.

*   The get_pipeline_run_status(run_id: str) async method must call get_pipeline_run(run_id) and return the pipeline run's status as a string.

*   The refresh_conn() async method must call get_async_conn() to recreate the connection.

*   The close() async method must call close() on the underlying connection object referenced by _async_conn and then set _async_conn to None.

*   AzureSynapsePipelineAsyncHook must support use as an async context manager; upon exiting the context, close() must be called, which closes the underlying connection and sets _async_conn to None.


*   Interface details: Type: Class
Name: AzureSynapsePipelineAsyncHook
Location: providers/microsoft/azure/src/airflow/providers/microsoft/azure/hooks/synapse.py
Description: An asynchronous hook to interact with Azure Synapse Pipeline. Must extend AzureSynapsePipelineHook.
Signature: __init__(self, azure_synapse_workspace_dev_endpoint: str, azure_synapse_conn_id: str = AzureSynapsePipelineHook.default_conn_name) -> None

Instance attribute: _async_conn
  Type: AsyncArtifactsClient | None
  Initial value: None
  Description: Holds the async connection client. Tests directly read and write this attribute to verify connection lifecycle.

Methods:
  get_async_conn(self) -> AsyncArtifactsClient  [async]
    Description: Returns (or creates) the async connection. With client secret credentials, must call AsyncClientSecretCredential(client_id=..., client_secret=..., tenant_id=...) and AsyncArtifactsClient(endpoint=..., credential=...). With default credentials, must call AsyncDefaultAzureCredential(managed_identity_client_id=..., workload_identity_tenant_id=...) and AsyncArtifactsClient(endpoint=..., credential=...). Must raise ValueError with message containing 'Tenant ID' when login/password present but tenantId is missing.

  get_pipeline_run(self, run_id: str) -> PipelineRun  [async]
    Description: Retrieves a pipeline run by its run ID using the async connection.

  get_pipeline_run_status(self, run_id: str) -> str  [async]
    Description: Calls get_pipeline_run(run_id) and returns the status as a string.

  refresh_conn(self) -> AsyncArtifactsClient  [async]
    Description: Forces recreation of the async connection by calling get_async_conn().

  close(self) -> None  [async]
    Description: Calls close() on the object stored in _async_conn, then sets _async_conn to None.

  __aenter__(self)  [async]
    Description: Async context manager entry; returns self.

  __aexit__(self, exc_type, exc_val, exc_tb)  [async]
    Description: Async context manager exit; calls close().

Required imports to add to the module:
  from azure.identity.aio import (
      ClientSecretCredential as AsyncClientSecretCredential,
      DefaultAzureCredential as AsyncDefaultAzureCredential,
  )
  from azure.synapse.artifacts.aio import ArtifactsClient as AsyncArtifactsClient


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.