## Description

Our model and chain deployment APIs currently require callers to explicitly pass a client version string and a trust flag on every API call. These values are error-prone to supply correctly, create unnecessary coupling between callers and internal versioning details, and make the API surface larger than it needs to be. We also currently use a form-encoded body for API requests, which is less idiomatic than JSON for a GraphQL endpoint.

We should refactor the deployment API so that client environment metadata is collected automatically and attached to requests without any involvement from the caller. This means removing the version and trust parameters from all public-facing deployment methods and from the supporting data structures (model version creation, chain deployment data, etc.).

## Expected Behavior

- Callers of model deployment and chain deployment methods should not need to pass a client version or a trust flag — those parameters should be gone from the API
- Client environment information should be automatically gathered and sent with every API request as a structured variable
- All GraphQL API requests should use a standard JSON body format rather than form-encoded data
- The chain deployment GraphQL mutation should pass environment context via a query variable rather than inlining it as a string literal
- An internal Python version resolution helper should be made private (not part of the public module surface)
- A plugin configuration for a model inference acceleration framework should drop support for a legacy option that is no longer needed

## Why This Matters

Removing these parameters simplifies how callers interact with the deployment API and eliminates a class of bugs where version strings were passed incorrectly or inconsistently. Centralizing environment metadata collection also gives the system a single, consistent place to control what is sent with every deployment request.
