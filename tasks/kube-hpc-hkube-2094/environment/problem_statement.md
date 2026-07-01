## Description

When algorithm jobs fail to start due to invalid container configurations — for example when a sidecar container specifies resource requests that exceed its declared resource limits — the system silently discards these failures. Pipeline operators have no visibility into why affected nodes never start running. The job creation failure is currently swallowed and not reported back through the scheduling pipeline.

Additionally, the flag used to indicate whether a scheduling timeout has been surpassed was named in a confusing way that implied capacity checking rather than timeout behavior. This naming inconsistency made the API harder to understand.

## Expected Behavior

- When the container orchestrator rejects a job creation request with a validation error (such as resource requests exceeding limits), the failure must be captured and recorded in the unscheduled algorithms list with an appropriate failure reason, a user-friendly formatted error message, and a flag indicating the timeout has been surpassed.
- The raw infrastructure error message must be reformatted to be human-readable: technical path expressions with numeric container indices should be replaced with the actual container name, and unnecessary formatting artifacts such as quoted numeric values should be cleaned up.
- Pipeline nodes whose algorithms fail due to job creation errors or invalid volume references must be marked as failed with a descriptive error message, rather than being left in an ambiguous state.
- The field tracking whether the scheduling timeout threshold has been crossed must be renamed to more accurately reflect its meaning — from a name that implied capacity checking to one that reflects timeout behavior.

## Why This Matters

Without this fix, algorithms that are misconfigured (e.g., sidecar resource limits set lower than requests) will silently fail to start. Pipeline runs appear to stall with no explanation, making it very difficult to diagnose and fix configuration problems. With these changes, operators get clear, actionable error messages explaining why a pipeline node could not be started.
