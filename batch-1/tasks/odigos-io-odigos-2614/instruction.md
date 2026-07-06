Implement a rule-based engine for a trace sampling processor to decide which traces to keep or drop based on configurable rules. Develop two types of sampling rules: endpoint-specific and global, ensuring endpoint rules take precedence. Provide a test utility library to facilitate trace construction for testing purposes.

Requirements:

* Implement the `ErrorRule` struct:
    * Ensure `ErrorRule.Evaluate` always returns `matched=true`.
    * Return `satisfied=true` and `fallback=0.0` if any span in the trace has an error status code.
    * Return `satisfied=false` and `fallback` equal to `FallbackSamplingRatio` if no error spans are found.

* Implement the `HttpRouteLatencyRule` struct:
    * Return `matched=false`, `satisfied=false`, and `fallback=0.0` if no resource matches `ServiceName`.
    * Return `matched=false`, `satisfied=false`, and `fallback=0.0` if the service matches but no span has an `http.route` attribute matching `HttpRoute`.
    * Return `matched=true`, `satisfied=true`, and `fallback=0.0` if a resource matches `ServiceName`, a span matches `HttpRoute`, and latency exceeds `Threshold`.
    * Return `matched=true`, `satisfied=false`, and `fallback` equal to `FallbackSamplingRatio` if service and route match but latency is at or below `Threshold`.
    * Handle traces with multiple resources, matching only against the resource with `ServiceName`.

* Develop the `RuleEngine`:
    * Use `NewRuleEngine` to accept a `Config` pointer with `EndpointRules` and `GlobalRules` slices, returning a `RuleEngine`.
    * Ensure `RuleEngine.ShouldSample` evaluates endpoint rules before global rules.
    * Return `true` if any endpoint rule is satisfied (`matched=true`, `satisfied=true`).
    * Use global rules only if no endpoint rule matches.
    * Return `true` if a global rule is not satisfied but its `FallbackSamplingRatio` is 100.
    * Return `false` if no rules match and all fallback ratios are 0.
    * Return `true` if multiple endpoint rules are configured and at least one is satisfied.

* Provide a `testutil` package:
    * Implement a fluent trace builder with `NewTrace`, `AddResource`, `AddSpan`, `Done`, and `Build`.
    * Provide span option functions: `WithStatus`, `WithAttribute`, and `WithLatency`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.