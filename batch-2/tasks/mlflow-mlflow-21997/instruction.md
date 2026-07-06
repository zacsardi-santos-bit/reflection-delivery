I'm working on adding a dedicated Databricks provider to the MLflow gateway.

*   DatabricksConfig must be instantiatable with no arguments; its four fields (host, token, client_id, client_secret) must all default to None and accept string values or None

*   DatabricksProvider must expose a NAME class attribute with value 'Databricks'

*   DatabricksProvider._api_base property must return a string of the form '<host>/serving-endpoints' where <host> is the workspace URL with any trailing slash removed (e.g., 'https://my-workspace.databricks.com/serving-endpoints')

*   DatabricksProvider.headers property must return the authentication headers dict obtained from the Databricks SDK workspace client's config.authenticate() method

*   DatabricksProvider._get_workspace_client() must instantiate databricks.sdk.WorkspaceClient passing only the non-None config fields as keyword arguments; if host and token are set, it must call WorkspaceClient(host=..., token=...); if host, client_id, and client_secret are set, it must call WorkspaceClient(host=..., client_id=..., client_secret=...); if the config is empty (all fields None), it must call WorkspaceClient() with no arguments

*   DatabricksProvider._workspace_client must be a writable instance attribute, initialized to None, allowing external injection of a pre-built workspace client

*   DatabricksProvider must support async chat requests: given a chat.RequestPayload, it must return a response whose encoded form includes the id field and the message content from the upstream API response

*   DatabricksProvider must support async embeddings requests: given an embeddings.RequestPayload, it must return a response whose encoded form includes the embedding vector in data[0].embedding

*   When a gateway endpoint with provider type 'databricks' is resolved via get_provider(), the result must be an instance of DatabricksProvider and its config.model.config must be an instance of DatabricksConfig

*   DatabricksProvider must be registered in the provider registry for both the Provider.DATABRICKS and Provider.DATABRICKS_MODEL_SERVING provider types so that Databricks endpoints are routed to DatabricksProvider rather than the generic fallback

*   The gateway endpoint-building logic must construct a DatabricksConfig (not a LiteLLMConfig) when handling a databricks provider endpoint, so that provider.config.model.config is a DatabricksConfig instance


*   Interface details: Type: Class
Name: DatabricksConfig
Location: mlflow/gateway/providers/databricks.py
Description: Configuration model for the Databricks provider. All fields are optional; when omitted, the Databricks SDK resolves credentials automatically from the environment.
Fields:
  host: str | None = None
  token: str | None = None
  client_id: str | None = None
  client_secret: str | None = None

Type: Class
Name: DatabricksProvider
Location: mlflow/gateway/providers/databricks.py
Description: Gateway provider for Databricks that uses the Databricks SDK for authentication and routes requests to Databricks Model Serving endpoints.
Class attributes:
  NAME: str = "Databricks"
  CONFIG_TYPE: type = DatabricksConfig
Instance attributes:
  _workspace_client: mutable, initially None — may be set directly to inject a workspace client
Methods/Properties:
  __init__(self, config: EndpointConfig, enable_tracing: bool = False) -> None
    Sets self.config, self._enable_tracing, self._provider_config = config.model.config, self._workspace_client = None
  _get_workspace_client(self) -> WorkspaceClient
    Returns a cached Databricks SDK WorkspaceClient. On first call, instantiates WorkspaceClient by passing only the non-None fields of the DatabricksConfig as keyword arguments. If all fields are None, calls WorkspaceClient() with no arguments.
  _api_base (property) -> str
    Returns the Databricks Model Serving base URL derived from the workspace client's configured host, normalized to end with '/serving-endpoints'. Example: "https://my-workspace.databricks.com/serving-endpoints"
  headers (property) -> dict[str, str]
    Returns authentication headers by calling workspace_client.config.authenticate(). Example: {"Authorization": "Bearer <token>"}
  chat(payload: chat.RequestPayload) -> chat.ResponsePayload  [async]
    Sends a chat request to the Databricks serving endpoint and returns the response.
  embeddings(payload: embeddings.RequestPayload) -> embeddings.ResponsePayload  [async]
    Sends an embeddings request to the Databricks serving endpoint and returns the response.

Note: DatabricksProvider must also be registered in the provider registry (mlflow/gateway/provider_registry.py) for the Provider.DATABRICKS and Provider.DATABRICKS_MODEL_SERVING provider types so that gateway endpoints with provider "databricks" resolve to DatabricksProvider instances.

Note: The gateway endpoint-building logic (mlflow/server/gateway_api.py) must be updated to handle the databricks provider type by constructing a DatabricksConfig (instead of falling through to LiteLLMConfig), so that provider.config.model.config is an instance of DatabricksConfig.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.