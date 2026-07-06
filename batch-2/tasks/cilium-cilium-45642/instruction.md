I'm working on the load balancer writer in the Cilium networking stack and I've found a bug in how backend selection handles zone-preference routing when health checking is involved.

*   The SelectBackends method must fall back to returning all backends when topology-aware 'prefer close' routing is active with health checking enabled and all backends in the local zone are unhealthy. Specifically, unhealthy backends (Unhealthy=true) must not count as valid same-zone candidates when determining whether zone-local preference applies.

*   When health checking is enabled on a service and all same-zone backends are unhealthy, SelectBackends must return backends from all zones (including the unhealthy local-zone backends and healthy remote-zone backends), yielding a result set that includes every available backend.

*   Backends with Unhealthy=true must be excluded from the same-zone candidate check even when their State is BackendStateActive, so that a quarantined or degraded local backend does not suppress fallback to healthy remote backends.

*   The testParams struct in pkg/loadbalancer/writer/writer_test.go must include a LocalNodeStore field of type *node.LocalNodeStore, and the fixture() helper function must populate this field so tests can update the local node's zone label to enable topology-aware routing in unit tests.


*   Interface details: Type: Method
Name: SelectBackends
Location: pkg/loadbalancer/writer/writer.go
Signature: SelectBackends(txn statedb.ReadTxn, bes iter.Seq2[*loadbalancer.Backend, statedb.Revision], svc *loadbalancer.Service, fe *loadbalancer.Frontend) iter.Seq2[*loadbalancer.Backend, statedb.Revision]
Description: Selects backends for a frontend/service combination. When topology-aware routing with TrafficDistributionPreferClose is active and service health checking is enabled, backends in the local zone that have Unhealthy=true must NOT be treated as valid same-zone candidates. If all same-zone backends are unhealthy (or no healthy same-zone candidates exist), the method must fall back to returning all backends regardless of zone.

Type: Struct field addition
Name: LocalNodeStore
Location: pkg/loadbalancer/writer/writer_test.go (testParams struct)
Signature: LocalNodeStore *node.LocalNodeStore
Description: The testParams struct used by the fixture() function must include a LocalNodeStore field of type *node.LocalNodeStore. The fixture() function must populate this field so that tests can call LocalNodeStore.Update() to set the local node's zone label (corev1.LabelTopologyZone) for topology-aware routing tests.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.