## Description

The MLflow gateway currently handles Databricks Model Serving endpoints by routing them through a generic third-party integration layer. This approach doesn't natively support the full range of Databricks authentication options (such as OAuth service principal credentials or environment-based credential discovery) and uses an indirect configuration mechanism that doesn't align with how other first-party providers are handled.

We should replace this with a dedicated first-party Databricks provider that uses the Databricks SDK directly for authentication and endpoint routing.

## Expected Behavior

- A dedicated Databricks provider class is available that uses the Databricks SDK for authentication
- The provider's configuration supports four optional fields: workspace host URL, personal access token, OAuth client ID, and OAuth client secret — all optional so the SDK's default credential chain can be used
- When a gateway endpoint is configured with the Databricks provider type, the resolved provider instance is the new Databricks-specific provider (not the generic fallback)
- The provider's base URL is automatically normalized to append the required serving endpoint path
- Authentication headers are obtained directly from the SDK's credential resolution mechanism
- The provider supports both chat and embeddings requests
- When credentials are explicitly configured, they are passed directly to the SDK; when omitted, the SDK resolves them automatically from the environment

## Why This Matters

This change enables users to configure Databricks Model Serving endpoints using the full range of Databricks authentication methods — including OAuth M2M for service principals and environment-based credential discovery — without needing to manually configure low-level integration details. It also makes the Databricks provider consistent with other first-party providers in the gateway.
