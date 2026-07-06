## Description

The webhook proxy service has two related shortcomings that need to be addressed:

1. **Missing Git URI in pipeline configuration**: When a new pipeline configuration is created for a repository, the generated configuration does not include the Git repository URI. The URI must be derived from the event data and included in the configuration so that the build system knows where to fetch the source code.

2. **Forward response is discarded**: When the proxy forwards a webhook event to the build system, the response from the build system is currently thrown away. Callers and downstream users of the service never receive the actual response from the build trigger, which means they cannot inspect the result of triggering a build.

## Expected Behavior

- The event data structure should carry the Git repository URI so it can be passed through the system and used when generating pipeline configurations.
- Generating a pipeline configuration should use a proper templating approach, substituting the pipeline name, trigger secret, Git URI, and branch into the configuration template.
- When a webhook is forwarded to the build system, the raw response from the build system should be returned to the original caller.
- The pipeline configuration template file should be a proper template with named placeholders for all dynamic fields.

## Why This Matters

Without these changes, triggered builds produce no feedback to the caller, and pipeline configurations created by the proxy may be incomplete or incorrect, leading to build failures due to missing source repository information.
