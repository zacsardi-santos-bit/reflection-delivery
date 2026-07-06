I'm building a trace sampling processor and need to add a rule-based engine that decides whether to keep or drop a given trace. Right now there's no logic for evaluating sampling rules, so I can't express policies like "sample traces from a specific HTTP route when they're slow" or "always keep traces with error spans."

I need two types of sampling rules. The first is an endpoint-specific rule that targets a particular service name and HTTP route with a latency threshold — if a trace has a span from the right service and route that exceeds the threshold, it should be sampled. If the service and route match but the latency is within the threshold, a fallback sampling ratio should apply. If the service or route don't match at all, the rule should be considered not applicable.

The second is a global rule that applies to all traces and samples any trace containing at least one error span. If no error is found, the rule falls back to a configured sampling percentage.

The rule engine itself should support two tiers: endpoint-level rules (evaluated first, with higher priority) and global rules (used as a fallback when no endpoint rule matches). When any endpoint rule matches the trace — even if the primary condition isn't met — the global rules should not override the result. If any matching rule is satisfied, or if a fallback ratio causes the trace to be included, the engine should return that the trace should be sampled.

I'd also like a small test utility library to help build traces with specific service names, span attributes, status codes, and latency values, so the rules can be tested in isolation.
