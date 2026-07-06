Develop a component that synchronizes Kubernetes alerting and recording rule resources with a Loki ruler instance. Implement logic to detect changes in Kubernetes rules and apply only necessary updates to Loki, ensuring efficient synchronization.

*   Implement the `diffRuleState` function:
    *   Accept two maps of namespace-to-rule-group-slices (desired and actual).
    *   Return a map of namespace-to-diff-slices indicating add, remove, or update operations.
    *   Ensure no output for identical namespaces in both maps.

*   Define the `ruleGroupDiff` struct with:
    *   Fields: `Kind` (type `ruleGroupDiffKind`), `Desired` (`rulefmt.RuleGroup`), `Actual` (`rulefmt.RuleGroup`).
    *   `ruleGroupDiffKind` constants: `ruleGroupDiffKindAdd`, `ruleGroupDiffKindRemove`, `ruleGroupDiffKindUpdate`.

*   Implement the `equalRuleGroups` function:
    *   Accept two `rulefmt.RuleGroup` values.
    *   Return true if they are semantically equal, false otherwise.

*   Ensure the `event` struct is hashable:
    *   Include a `typ` field of type `eventType`.
    *   Define `eventTypeSyncLoki` as a constant of type `eventType`.

*   Parse the `Arguments` struct from a river configuration:
    *   Include fields: `Address`, `LokiNameSpacePrefix`, `HTTPClientConfig`.
    *   Validate mutual exclusivity of authentication methods, returning an error if violated.

*   Define the `Component` struct with:
    *   Fields: `log`, `queue`, `namespaceLister`, `namespaceSelector`, `ruleLister`, `ruleSelector`, `lokiClient`, `args`, `metrics`.

*   Implement `newQueuedEventHandler`:
    *   Accept a `log.Logger` and a `workqueue.RateLimitingInterface`.
    *   Return a handler implementing `cache.ResourceEventHandler` with `OnAdd`, `OnUpdate`, `OnDelete` methods.

*   Create `lokiNamespaceForRuleCRD` function:
    *   Accept a prefix string and a `*promv1.PrometheusRule`.
    *   Return a unique string identifying the Loki namespace for the rule resource.

*   Implement `newMetrics` to return a `*metrics` struct.

*   Develop the `Component.eventLoop` method:
    *   Process events from the queue to reconcile Kubernetes state with Loki ruler state.

*   Implement `buildRequest` in `pkg/loki/client`:
    *   Accept operation name, path string, HTTP method, base `url.URL`, and byte payload.
    *   Ensure correct URL joining and percent-encoding handling.

*   Define the `Interface` type in `pkg/loki/client` with methods:
    *   `CreateRuleGroup`, `DeleteRuleGroup`, `ListRules`.

*   Implement the `New` function in `pkg/loki/client`:
    *   Accept a `log.Logger`, a `Config`, and a `*prometheus.HistogramVec`.
    *   Return a `(*LokiClient, error)`.

*   Ensure `DeleteRuleGroup` method encodes URL paths correctly:
    *   Use `url.PathEscape` for encoding.

*   Define `OperationNameContextKey` in `pkg/loki/client/internal`.

*   Implement `NewTimedClient` and `operationName` in `pkg/loki/client/internal`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.