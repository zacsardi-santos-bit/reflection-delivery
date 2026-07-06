Implement support for specifying the instance edition and autoscaling configuration in the SpannerInstance resource managed by Config Connector. Ensure that the observed state includes the current node count and processing units when autoscaling is active.

*   Update `SpannerInstanceSpec` in `apis/spanner/v1beta1/instance_types.go`:
    *   Add an optional `Edition` field with type `*string` and JSON tag `edition,omitempty`. Valid values: EDITION_UNSPECIFIED, STANDARD, ENTERPRISE, ENTERPRISE_PLUS.
    *   Add an optional `AutoscalingConfig` field with type `*AutoscalingConfig` and JSON tag `autoscalingConfig,omitempty`. This should include `autoscalingLimits` (maxNodes, minNodes, maxProcessingUnits, minProcessingUnits) and `autoscalingTargets` (highPriorityCpuUtilizationPercent, storageUtilizationPercent).

*   Update `SpannerInstanceObservedState` in `apis/spanner/v1beta1/instance_types.go`:
    *   Uncomment and enable `NumNodes` field with type `*int32` and JSON tag `numNodes,omitempty`.
    *   Uncomment and enable `ProcessingUnits` field with type `*int32` and JSON tag `processingUnits,omitempty`.

*   Ensure that when updating a `SpannerInstance`, if the `edition` field has changed, it is included in the update request's fieldMask to the Spanner API.

*   Modify the CRD definition in `config/crds/resources/apiextensions.k8s.io_v1_customresourcedefinition_spannerinstances.spanner.cnrm.cloud.google.com.yaml`:
    *   Expose `autoscalingConfig` and `edition` in the spec.
    *   Include `numNodes` and `processingUnits` in the status.observedState.

*   Update `tests/apichecks/testdata/exceptions/missingfields.txt` to include:
    *   "[missing_field] crd=spannerinstances.spanner.cnrm.cloud.google.com version=v1beta1: field \".spec.autoscalingConfig.autoscalingLimits.maxProcessingUnits\" is not set in unstructured objects"
    *   "[missing_field] crd=spannerinstances.spanner.cnrm.cloud.google.com version=v1beta1: field \".spec.autoscalingConfig.autoscalingLimits.minProcessingUnits\" is not set in unstructured objects"

*   Create a new test fixture directory `spannerinstance-direct-full` in `pkg/test/resourcefixture/testdata/basic/spanner/v1beta1/spannerinstance-direct-full/`:
    *   Include `create.yaml` specifying `edition: ENTERPRISE` with autoscaling config.
    *   Include `update.yaml` specifying `edition: ENTERPRISE_PLUS` with updated autoscaling targets.
    *   Include `_generated_object_spannerinstance-direct-full.golden.yaml` with expected status showing `observedState.numNodes` and `observedState.processingUnits`.
    *   Include `_http.log` for recorded API interactions.

*   Update the golden YAML for existing fixtures (`spannerinstance`, `spannerinstance-full`) to include `status.observedState.numNodes` and `status.observedState.processingUnits`. Ensure `spannerinstance-basic` golden YAML includes an empty `status.observedState`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.