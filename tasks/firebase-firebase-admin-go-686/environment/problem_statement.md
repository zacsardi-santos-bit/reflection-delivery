## Description

The Firebase Remote Config Go Admin SDK currently fetches and caches server templates but cannot evaluate the conditions defined within those templates. As a result, calling the evaluation method always returns static parameter defaults regardless of which conditions may be active — there is no way to target different parameter values to different users or sessions based on percentage rollouts or other condition logic.

This means server-side Go applications cannot take advantage of conditional configuration values. A template might define a condition that targets 10% of users to receive an experimental feature flag, but the SDK will return the default value for every request.

## Expected Behavior

- The template evaluation method should accept a context map (containing at minimum a user/device randomization identifier) and return parameter values that reflect the active conditions for that context.
- Condition logic should support OR (true if any sub-condition is true) and AND (true if all sub-conditions are true) compound conditions with short-circuit evaluation.
- Percentage-based conditions should deterministically assign users to buckets using a hash of the seed and randomization identifier, supporting "less than or equal to", "greater than", and "between range" comparisons.
- When a condition evaluates to true and the parameter is configured to use the in-app default, the SDK should fall back to the caller-supplied default config and report the value source accordingly.
- The evaluation method should return an error if no template has been loaded into the cache yet.
- The default configuration values provided by the caller should be stored as plain strings rather than JSON-encoded strings — a string value should be stored verbatim, not wrapped in extra quotation marks the way JSON encoding would produce.
- The template's JSON representation should use the correct lowercase field name for the version tag field.

## Why This Matters

Without server-side condition evaluation, the Go Admin SDK cannot be used for dynamic personalization or gradual feature rollouts. Developers targeting specific user segments through Remote Config conditions would get no benefit from the Go SDK, making it significantly less useful than its counterparts on other platforms.
