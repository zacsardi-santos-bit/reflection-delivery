## Description

The Horizontal Pod Autoscaler controller does not clearly distinguish between the different reasons why a scaling decision was capped or adjusted. When metrics indicate that many more pods are needed than the configured maximum allows, the system should explicitly report that the configured maximum replicas was the limiting factor — not the rate-limiter designed to prevent too-rapid scaling. Similarly, when scaling is limited by the scale-up rate rather than the configured ceiling, that distinction should be reported clearly.

Currently, the logic that normalizes a desired replica count to a final value is embedded in a way that makes it hard to reason about and test independently. Extracting this into a dedicated, rule-based function would make the behavior explicit and testable.

## Expected Behavior

- When the calculated desired replica count falls within the configured min/max range, scaling proceeds normally with no limiting condition reported.
- When the desired count is below the minimum, the actual count should be raised to the minimum and the reason should be reported.
- When the desired count is zero or below and the minimum is also configured as zero, an absolute floor of one replica should still be enforced.
- When the desired count exceeds the configured maximum replicas (even if the scale-up rate limiter would allow more), the controller must cap at maxReplicas and report the configured maximum as the limiting reason.
- When the desired count exceeds only the scale-up rate limit (but not the configured maximum), the controller must cap at the rate limit and report that as the limiting reason.

## Why This Matters

Without this distinction, operators have no reliable way to understand whether their configured replica maximum is actually constraining the autoscaler versus the built-in scale-up rate limiter. This makes it harder to tune HPA settings and diagnose scaling behavior.
