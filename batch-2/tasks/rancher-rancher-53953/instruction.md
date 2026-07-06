I'm working on the RBAC aggregation feature in Rancher and I've run into a design issue with how resources are labeled.

*   Must add a new exported constant AggregationManagementFeatureLabel with the value "management.cattle.io/roletemplate-aggregation-mgmt" to the pkg/rbac package, distinct from the existing AggregationFeatureLabel ("management.cattle.io/roletemplate-aggregation").

*   AddAggregationManagementFeatureLabel must accept any Kubernetes object (metav1.Object) and set the rbac.AggregationManagementFeatureLabel key to the string "true" on its labels; if the label map is nil it must be initialized; if the key already exists it must be overwritten.

*   AddAggregationFeatureLabel must accept any Kubernetes object (metav1.Object) and set the rbac.AggregationFeatureLabel key to the string "true" on its labels; if the label map is nil it must be initialized; if the key already exists it must be overwritten.

*   BuildAggregatingClusterRole must NOT include the label key "management.cattle.io/roletemplate-aggregation" in the returned ClusterRole's labels; only the "management.cattle.io/aggregates" and "authz.cluster.cattle.io/clusterrole-owner" labels should remain.

*   BuildAggregatingRoleBindingFromRTB must NOT automatically add the "management.cattle.io/roletemplate-aggregation" label to the returned RoleBinding; the aggregation label previously set inside this function must be removed so that callers can apply the correct label explicitly after building the binding.

*   BuildAggregatingClusterRoleBindingFromRTB must NOT automatically add the "management.cattle.io/roletemplate-aggregation" label to the returned ClusterRoleBinding; the aggregation label previously set inside this function must be removed so that callers can apply the correct label explicitly after building the binding.

*   The crtbHandler method previously named removeRoleBindings must be renamed to deleteRoleBindings; all callers and internal references must be updated to use the new name.

*   The crtbHandler struct must include a clusterController field for v3.Cluster resources; when handleMigration is invoked with the feature flag disabled and the aggregation label present on the CRTB, it must call deleteRoleBindings AND additionally invoke downstream resource cleanup (using clusterController.Get with the CRTB's ClusterName) after removing the label.

*   The prtbHandler struct in pkg/controllers/management/auth/roletemplates must include a clusterController field for v3.Cluster resources; when handleMigration is invoked with the feature flag disabled and the aggregation label present on the PRTB, it must call deleteRoleBindings AND additionally invoke downstream resource cleanup (using clusterController.Get with the cluster name derived from the PRTB's ProjectName) after removing the label.

*   prtbHandler.ensureOnlyDesiredRoleBindingExists must accept a *v3.ProjectRoleTemplateBinding as its second argument (replacing the two separate label string parameters); it must internally derive the owner label selector as "<prtb-owner-label>=true,management.cattle.io/roletemplate-aggregation=true" and the legacy label selector as "authz.cluster.cattle.io/rtb-owner-updated=_<prtb-name>".

*   prtbHandler.ensureOnlyDesiredClusterRoleBindingsExists must accept a *v3.ProjectRoleTemplateBinding as its second argument (replacing the two separate label string parameters); it must internally derive label selectors from the PRTB.

*   Management-plane listing operations for role bindings (in pkg/controllers/management/auth/roletemplates) must use a combined label selector of the form "<owner-label>=true,management.cattle.io/roletemplate-aggregation-mgmt=true"; role bindings created by management-plane handlers must carry the "management.cattle.io/roletemplate-aggregation-mgmt": "true" label.

*   Management-plane listing operations for cluster roles (in pkg/controllers/management/auth/roletemplates) must use a combined label selector of the form "authz.cluster.cattle.io/clusterrole-owner=<rt-name>,management.cattle.io/roletemplate-aggregation-mgmt=true"; cluster roles created by management-plane handlers must carry the "management.cattle.io/roletemplate-aggregation-mgmt": "true" label.

*   Downstream (user cluster) listing operations for role bindings and cluster roles (in pkg/controllers/managementuser/rbac/roletemplates) must use a combined label selector joining the RTB owner label with "management.cattle.io/roletemplate-aggregation=true"; for example "authz.cluster.cattle.io/crtb-owner-<name>=true,management.cattle.io/roletemplate-aggregation=true" or "authz.cluster.cattle.io/prtb-owner-<name>=true,management.cattle.io/roletemplate-aggregation=true".

*   Downstream (user cluster) listing operations for cluster roles owned by a RoleTemplate must use the combined selector "authz.cluster.cattle.io/clusterrole-owner=<rt-name>,management.cattle.io/roletemplate-aggregation=true" (previously only the clusterrole-owner label was used).


*   Interface details: Type: Constant
Name: AggregationManagementFeatureLabel
Location: pkg/rbac/common.go (or any file in pkg/rbac)
Signature: AggregationManagementFeatureLabel = "management.cattle.io/roletemplate-aggregation-mgmt"
Description: New exported string constant used to label management-plane aggregation resources (role bindings and cluster roles) with a value distinct from the downstream cluster aggregation label.

Type: Function
Name: AddAggregationManagementFeatureLabel
Location: pkg/controllers/management/auth/roletemplates/common.go
Signature: AddAggregationManagementFeatureLabel(obj metav1.Object)
Description: Sets the rbac.AggregationManagementFeatureLabel key to "true" on the given Kubernetes object's labels. Initializes the label map if it is nil. Overwrites any existing value for that key.

Type: Function
Name: AddAggregationFeatureLabel
Location: pkg/controllers/managementuser/rbac/roletemplates/common.go
Signature: AddAggregationFeatureLabel(obj metav1.Object)
Description: Sets the rbac.AggregationFeatureLabel key to "true" on the given Kubernetes object's labels. Initializes the label map if it is nil. Overwrites any existing value for that key.

Type: Method
Name: deleteRoleBindings
Location: pkg/controllers/management/auth/roletemplates/crtb_handler.go
Signature: deleteRoleBindings(crtb *v3.ClusterRoleTemplateBinding) error
Description: Deletes management-plane role bindings associated with the given CRTB. This method was previously named removeRoleBindings; callers and internal references must use the new name deleteRoleBindings.

Type: Struct field
Name: clusterController
Location: pkg/controllers/management/auth/roletemplates/crtb_handler.go (crtbHandler struct)
Signature: clusterController controllersv3.ClusterController (or equivalent interface for v3.Cluster resources)
Description: Controller for v3.Cluster resources, required so that handleMigration can call deleteDownstreamResources when the aggregation feature flag is disabled.

Type: Struct field
Name: clusterController
Location: pkg/controllers/management/auth/roletemplates/prtb_handler.go (prtbHandler struct)
Signature: clusterController controllersv3.ClusterController (or equivalent interface for v3.Cluster resources)
Description: Controller for v3.Cluster resources, required so that handleMigration can call deleteDownstreamResources when the aggregation feature flag is disabled.

Type: Method
Name: ensureOnlyDesiredRoleBindingExists
Location: pkg/controllers/managementuser/rbac/roletemplates/prtb_handler.go (prtbHandler struct)
Signature: ensureOnlyDesiredRoleBindingExists(desiredRB *rbacv1.RoleBinding, prtb *v3.ProjectRoleTemplateBinding) error
Description: Updated signature — second argument is now *v3.ProjectRoleTemplateBinding instead of separate ownerLabel and legacyOwnerLabel strings. The function derives the appropriate label selectors internally from the PRTB. The owner label selector used is "<authz.cluster.cattle.io/prtb-owner-<prtb-name>>=true,management.cattle.io/roletemplate-aggregation=true". The legacy label selector is "authz.cluster.cattle.io/rtb-owner-updated=_<prtb-name>".

Type: Method
Name: ensureOnlyDesiredClusterRoleBindingsExists
Location: pkg/controllers/managementuser/rbac/roletemplates/prtb_handler.go (prtbHandler struct)
Signature: ensureOnlyDesiredClusterRoleBindingsExists(crbs []*rbacv1.ClusterRoleBinding, prtb *v3.ProjectRoleTemplateBinding) error
Description: Updated signature — second argument is now *v3.ProjectRoleTemplateBinding instead of separate ownerLabel and legacyOwnerLabel strings. The function derives the appropriate label selectors internally from the PRTB.

Type: Function
Name: BuildAggregatingClusterRole
Location: pkg/rbac/common.go
Signature: (existing function, no signature change)
Description: Must NOT include the label "management.cattle.io/roletemplate-aggregation": "true" in the returned ClusterRole's labels. This label was previously included but must be removed. The returned ClusterRole retains the "management.cattle.io/aggregates" and "authz.cluster.cattle.io/clusterrole-owner" labels only.

Type: Function
Name: BuildAggregatingRoleBindingFromRTB
Location: pkg/rbac/common.go
Signature: (existing function, no signature change)
Description: Must NOT automatically add the aggregation feature label ("management.cattle.io/roletemplate-aggregation") to the returned RoleBinding. Previously this label was added inside this function; it must be removed. Callers are responsible for applying the appropriate aggregation label explicitly (e.g., via AddAggregationManagementFeatureLabel or AddAggregationFeatureLabel after the binding is created).

Type: Function
Name: BuildAggregatingClusterRoleBindingFromRTB
Location: pkg/rbac/common.go
Signature: (existing function, no signature change)
Description: Must NOT automatically add the aggregation feature label ("management.cattle.io/roletemplate-aggregation") to the returned ClusterRoleBinding. Previously this label was added inside this function; it must be removed. Callers are responsible for applying the appropriate aggregation label explicitly.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.