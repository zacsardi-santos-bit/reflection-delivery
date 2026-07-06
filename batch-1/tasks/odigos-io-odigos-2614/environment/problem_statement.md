## Description

The distributed trace sampling processor needs a rule-based engine to decide which traces to keep. Currently there is no structured mechanism for evaluating a trace against configurable sampling rules, so either all traces are kept or none are — there is no way to express conditions like "keep traces from a specific HTTP endpoint when they are slow" or "always keep traces that contain an error."

## Expected Behavior

The sampling system should support two categories of rules:

- **Endpoint-specific rules**: Target a particular service name and HTTP route with a configurable latency threshold. A trace is sampled if the matching service/route combination has a span whose duration exceeds the threshold. If the trace matches the service and route but doesn't exceed the threshold, the rule falls back to a configurable sampling percentage.
- **Global rules**: Apply to all traces regardless of service or route. For example, an error-based rule samples any trace containing at least one error span. If no error is found, the rule applies a fallback sampling ratio.

Endpoint-level rules must take priority over global rules when evaluating a trace. If an endpoint rule matches the trace (even if the primary condition is not satisfied), the global rules should not override the decision.

Each rule should expose an evaluation method that returns whether the rule matched the trace, whether the primary condition was satisfied, and what fallback sampling ratio to apply.

## Why This Matters

Without this capability, operators cannot selectively capture high-value traces (slow endpoints, error paths) while keeping overall trace volume manageable. A rule engine with fallback ratios allows fine-grained control: critical traces are always captured, and a configurable percentage of normal traces is sampled for baseline coverage.
