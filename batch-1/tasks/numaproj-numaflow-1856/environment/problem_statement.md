## Description

The platform has a concept of "readiness" for pipelines and inter-step buffer services, but no distinct concept of "health" that accounts for different operational lifecycle states. For pipelines, phases like pausing, paused, and deleting represent intentional and expected operational states — they are not failures. However, because the existing check only looks at readiness conditions, tooling and controllers that rely on a single status check cannot distinguish between "pipeline is broken" and "pipeline is intentionally pausing or paused." This causes misleading status signals during normal lifecycle transitions.

Similarly, the pipeline status summary does not distinguish between different kinds of user-defined processing functions. The existing UDF count treats all UDFs as the same type, making it impossible to tell at a glance how many are map-type versus reduce-type.

## Expected Behavior

- Both pipelines and inter-step buffer services should expose a health-check that reflects their actual operational state, not just their readiness conditions alone.
- For pipelines: pausing, paused, and deleting phases should report as healthy. The running phase should only be healthy if the pipeline is also ready. Failed and unknown phases should be unhealthy.
- For inter-step buffer services: health requires both the running phase and full readiness. A pending or otherwise non-running service is not healthy even if conditions appear met.
- The pipeline status should separately expose the count of map-type and reduce-type user-defined functions.
- The OpenAPI schema for pipeline status should include the new map and reduce UDF count fields.

## Why This Matters

Without a proper health concept, controllers and monitoring tools that depend on status checks may incorrectly report pipelines as unhealthy during routine lifecycle transitions (pausing, draining, etc.), causing unnecessary alerts or incorrect behavior. Separating map and reduce UDF counts gives operators better visibility into pipeline composition.
