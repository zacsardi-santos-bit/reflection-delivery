Implement support for an optional control plane outbound load balancer in private clusters. Ensure that this feature is configurable only for private clusters and is immutable after cluster creation. Validate configurations to prevent errors and enforce constraints.

*   Update the `NetworkSpec` struct:
    *   Add a new optional field `ControlPlaneOutboundLB` of type `*LoadBalancerSpec` with JSON tag `controlPlaneOutboundLB,omitempty`.

*   Implement `setControlPlaneOutboundLBDefaults` method in `api/v1alpha4/azurecluster_default.go`:
    *   For public clusters (`APIServerLB.Type == Public`), perform no operation on `ControlPlaneOutboundLB`.
    *   For private clusters (`APIServerLB.Type == Internal`):
        *   If `ControlPlaneOutboundLB` is `nil`, perform no operation.
        *   If `ControlPlaneOutboundLB` is non-nil, set defaults:
            *   Name: `{clusterName}-outbound-lb` if empty.
            *   SKU: `SKUStandard`.
            *   Type: `Public`.
            *   Populate `FrontendIPs` with entries named `{lb-name}-frontEnd-{i}` and `PublicIP.Name` as `pip-{clusterName}-controlplane-outbound-{i}`.

*   Implement `validateControlPlaneOutboundLB` function in `api/v1alpha4/azurecluster_validation.go`:
    *   Accept parameters: `(lb *LoadBalancerSpec, apiserverLB LoadBalancerSpec, fldPath *field.Path)`.
    *   Return `FieldValueForbidden` error at `controlPlaneOutboundLB` if `lb` is non-nil and `apiserverLB.Type` is `Public`.
    *   Return no errors if `lb` is `nil` and `apiserverLB.Type` is `Internal`.
    *   Return no errors for valid non-nil `lb` configurations in private clusters.
    *   Return `FieldValueInvalid` error at `controlPlaneOutboundLB.frontendIPsCount` if `FrontendIPsCount` exceeds 16.

*   Update `ValidateUpdate` method in `api/v1alpha4/azurecluster_webhook.go`:
    *   Reject any modifications to `Spec.NetworkSpec.ControlPlaneOutboundLB` after cluster creation, treating it as immutable.

*   Modify the CI private cluster template:
    *   In `templates/test/ci/cluster-template-prow-private.yaml`, include a `controlPlaneOutboundLB` section with `frontendIPsCount: 1` in the `networkSpec`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.