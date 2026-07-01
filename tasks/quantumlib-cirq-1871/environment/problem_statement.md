## Description

The Google quantum engine client currently uses an API key as its primary authentication mechanism, embedding it directly in request URLs. This is inconsistent with standard Google Cloud authentication practices, where a project ID is used for billing/quota tracking and OAuth credentials handle authentication — rather than API keys in URLs.

We need to refactor the engine client so that the project ID is provided at the client level (not repeated in every job configuration), and authentication uses a request builder that adds the project ID as a header for billing purposes.

## Expected Behavior

- The engine client should be initialized with a project ID, which it stores and reuses for all operations (listing processors, retrieving calibrations, etc.)
- Job configurations should no longer require a project ID — the client's own project ID should be used automatically
- When connecting to the API service, the project ID should be included as a request header rather than as an API key query parameter
- A custom discovery URL should be optionally configurable for connecting to alternate endpoints; this should be mutually exclusive with a version-only configuration (both together is an error)
- The convenience function that creates the engine from environment variables should look for a project ID variable, raising an informative error if it is absent

## Why This Matters

This change aligns the quantum engine client with standard Google Cloud API patterns: project-scoped authentication, request headers for billing attribution, and environment-based configuration. It also reduces repetition for users, who previously had to supply the project ID in every job configuration.
