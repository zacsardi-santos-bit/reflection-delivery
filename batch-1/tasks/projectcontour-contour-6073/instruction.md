Implement the ability to configure a provisioned gateway to watch routes only in specific namespaces. Update the RBAC setup to support namespace-scoped permissions and ensure that cluster roles can be restricted to cluster-scoped resources only.

*   Implement the `NamespacesToStrings` function:
    *   Accept a slice of `Namespace` values and return a slice of plain strings.
    *   Ensure an empty input slice returns an empty (non-nil) string slice.

*   Update the `DesiredDeployment` function:
    *   When `cntr.Spec.WatchNamespaces` is non-empty, include a `--watch-namespaces` argument in the contour container.
    *   Format the argument as `--watch-namespaces=<ns1>,<ns2>,...,<cntr.Namespace>`.

*   Modify the `desiredClusterRole` function:
    *   Accept a `clusterScopeOnly` boolean parameter.
    *   When `clusterScopeOnly` is true, ensure policy rules only cover `gatewayclasses`, `gatewayclasses/status`, and `namespaces`.
    *   When false, include additional rules for namespace-scoped resources.

*   Implement the `desiredRoleForResourceInNamespace` function:
    *   Create an RBAC Role with the specified `name` and `namespace`.
    *   Set owner labels using `model.ContourOwningGatewayNameLabel` and `model.GatewayAPIOwningGatewayNameLabel` keyed by `contour.Name`.

*   Implement the `desiredRoleBindingInNamespace` function:
    *   Create an RBAC RoleBinding with the specified `name` and `namespace`.
    *   Set owner labels using `model.ContourOwningGatewayNameLabel` and `model.GatewayAPIOwningGatewayNameLabel`.
    *   Include a Subject with Kind "ServiceAccount", using `svcAcctRef` for the name and `contour.Namespace` for the namespace.
    *   Set `RoleRef` to the provided `roleRef`.

*   Implement the `HTTPRouteIgnoredByContour` function:
    *   Return false if the `route` is nil.
    *   Return true if the route's `.status.parents` slice is empty, indicating it has not been reconciled by Contour.

*   Ensure provisioned gateways configured with watch namespaces:
    *   Accept HTTPRoutes in listed namespaces, routing traffic successfully (HTTP 200).
    *   Leave HTTPRoutes in unlisted namespaces unreconciled, with an empty `.status.parents` list.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.