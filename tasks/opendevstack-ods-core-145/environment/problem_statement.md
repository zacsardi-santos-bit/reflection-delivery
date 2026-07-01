## Description

The Jenkins webhook proxy currently only accepts webhook events pushed from BitBucket — there is no way to directly trigger a pipeline build via an HTTP request. Additionally, there is no mechanism to pass custom environment variables through the build process, which limits how builds can be configured or parameterized.

## Expected Behavior

- The proxy should expose a dedicated HTTP endpoint that accepts direct build requests, not just BitBucket webhook events.
- Direct build requests must include a shared secret for authorization; requests without the secret should be rejected with an unauthorized response.
- The caller should be able to specify the branch, project, repository, and optional environment variables in the request body.
- An optional query parameter should allow the caller to override the component name used to derive the pipeline name.
- When the pipeline is created, any environment variables provided in the request should be passed through to the build strategy configuration.
- The pipeline configuration template should include a flag that enables environment variable passing via the generic webhook trigger.
- Requests to unrecognized paths should return a not-found response rather than silently failing.

## Why This Matters

Teams need a programmatic way to trigger builds directly — for example, from scripts or other automation — without routing through a source control webhook. They also need the ability to inject environment variables into builds to control runtime behavior, which is a common requirement for parameterized CI/CD pipelines.
