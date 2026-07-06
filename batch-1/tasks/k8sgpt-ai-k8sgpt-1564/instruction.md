Implement analyzers for k8sgpt to detect issues with resources managed by the Operator Lifecycle Manager (OLM). Ensure these analyzers can identify and report problems with catalog sources, cluster service versions, install plans, operator groups, and subscriptions, while ignoring healthy resources.

*   Implement the `CatalogSourceAnalyzer` in `pkg/analyzer/catalogsource.go`.
    *   List all `CatalogSource` resources using the dynamic Kubernetes client.
    *   Return a result for each resource with a `status.connectionState.lastObservedState` that is non-empty and not 'READY'.
    *   Set the result Kind to 'CatalogSource', Name to 'namespace/resourceName', and Error[0].Text to the value of `lastObservedState`.
    *   Ignore resources with no status block or with `lastObservedState` equal to 'READY'.

*   Implement the `ClusterServiceVersionAnalyzer` in `pkg/analyzer/clusterserviceversion.go`.
    *   List all `ClusterServiceVersion` resources.
    *   Return a result for each resource with a `status.phase` not equal to 'Succeeded'.
    *   Set the result Kind to 'ClusterServiceVersion', Name to 'namespace/resourceName', and Error[0].Text to the condition message from `status.conditions`.
    *   Ignore resources with `status.phase` equal to 'Succeeded'.

*   Implement the `InstallPlanAnalyzer` in `pkg/analyzer/instalplan.go`.
    *   List all `InstallPlan` resources.
    *   Return a result for each resource with a `status.phase` not equal to 'Complete'.
    *   Set the result Kind to 'InstallPlan', Name to 'namespace/resourceName', and Error[0].Text to the condition reason from `status.conditions`.
    *   Ignore resources with `status.phase` equal to 'Complete'.

*   Implement the `OperatorGroupAnalyzer` in `pkg/analyzer/operatorgroup.go`.
    *   List all `OperatorGroup` resources and group them by namespace.
    *   Return one result per namespace containing more than one `OperatorGroup`.
    *   Set the result Kind to 'OperatorGroup' and Name to the namespace string.
    *   Ignore namespaces with only one `OperatorGroup`.

*   Implement the `SubscriptionAnalyzer` in `pkg/analyzer/subscription.go`.
    *   List all `Subscription` resources.
    *   Return a result for each resource with a `status.state` not equal to 'AtLatestKnown'.
    *   Set the result Kind to 'Subscription', Name to 'namespace/resourceName', and Error[0].Text to the condition reason from `status.conditions`.
    *   Ignore resources with `status.state` equal to 'AtLatestKnown'.

*   Ensure each analyzer implements the `common.IAnalyzer` interface with the signature `Analyze(a common.Analyzer) ([]common.Result, error)`.
*   Register each analyzer in `pkg/analyzer/analyzer.go` under the `additionalAnalyzerMap` with appropriate keys.
*   Use the dynamic Kubernetes client from the `common.Analyzer` argument for resource listing.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.