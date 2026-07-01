## Description

Currently, Datadog credentials for metric analysis in Argo Rollouts must be stored either as environment variables on the controller process, or in a single global secret in the namespace where the Argo Rollouts controller is deployed. This rigid model is inflexible for multi-tenant environments where different teams manage their own Datadog API credentials and do not have access to the controller's namespace.

## Expected Behavior

- Analysis templates should be able to reference a named secret within the same namespace as the template itself for Datadog credentials.
- When a secret reference is specified with the namespaced flag enabled, the system should look up that secret in the template's namespace rather than the controller's namespace.
- If a secret reference specifies namespaced lookup but no secret name is provided, the operation should fail with an error rather than silently falling back.
- If the referenced secret does not exist or cannot be found, the credential lookup should fail with an error.
- The credential lookup abstraction should support multiple credential sources, and each source should gracefully return empty values when credentials are unavailable so the next source can be tried.

## Why This Matters

Teams deploying Argo Rollouts in shared clusters need to manage their own Datadog credentials independently, without relying on cluster administrators to configure global secrets in the controller namespace. Allowing per-namespace secret references makes credential management self-service and more secure by scoping access appropriately.
