Refactor the endpoint network policy query feature to provide a clearer structure for users querying which network policies affect a specific pod. Implement changes to separate applied policies, ingress rules, and egress rules in the response, and relocate response types to the appropriate packages.

* Update the EndpointQuerier interface:
    * Replace the `QueryNetworkPolicies` method with `QueryNetworkPolicyRules(namespace, podName string) (*antreatypes.EndpointNetworkPolicyRules, error)` in `pkg/controller/networkpolicy/`.
    * Ensure `QueryNetworkPolicyRules` returns `(nil, nil)` if no pod matches the given namespace and name.
    * When a pod is found, return `EndpointNetworkPolicyRules` with:
        * `Namespace` and `Name` set to the queried values.
        * `AppliedPolicies` listing policies directly selecting the pod.
        * `EndpointAsIngressSrcRules` listing ingress rules referencing the pod's address group.
        * `EndpointAsEgressDstRules` listing egress rules referencing the pod's address group.

* Define new structs in `pkg/controller/types/`:
    * `EndpointNetworkPolicyRules` with fields: `Namespace string`, `Name string`, `AppliedPolicies []*controlplane.NetworkPolicyReference`, `EndpointAsIngressSrcRules []*RuleInfo`, `EndpointAsEgressDstRules []*RuleInfo`.
    * `RuleInfo` with fields: `Policy *controlplane.NetworkPolicyReference`, `Index int`, `Rule *controlplane.NetworkPolicyRule`.

* Define response types in `pkg/apiserver/handlers/endpoint/`:
    * `EndpointQueryResponse` with `Endpoints []Endpoint`.
    * `Endpoint` with fields: `Namespace string`, `Name string`, `AppliedPolicies []v1beta2.NetworkPolicyReference`, `IngressSrcRules []Rule`, `EgressDstRules []Rule`.
    * `Rule` with fields: `PolicyRef v1beta2.NetworkPolicyReference`, `Direction v1beta2.Direction`, `RuleIndex int`.

* Update the HTTP handler in `pkg/apiserver/handlers/endpoint/`:
    * Use the updated `EndpointQuerier` interface with `QueryNetworkPolicyRules`.
    * Return HTTP 404 if `QueryNetworkPolicyRules` returns `nil`.

* Implement a package-level function in `pkg/antctl/output/`:
    * `TableOutputForQueryEndpoint(obj interface{}, writer io.Writer) error` to format endpoint query results.
    * Use section headers: "Applied Policies on Endpoint:", "Egress Rules Referencing Endpoint as Destination:", "Ingress Rules Referencing Endpoint as Source:".
    * Indicate "None" when a section is empty.

* Modify the mock `MockEndpointQuerier` in `pkg/controller/networkpolicy/testing/mock_networkpolicy.go`:
    * Implement `QueryNetworkPolicyRules(arg0, arg1 string) (*types.EndpointNetworkPolicyRules, error)` instead of `QueryNetworkPolicies`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.