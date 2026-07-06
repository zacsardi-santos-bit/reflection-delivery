I'm working on the ztunnel integration in Cilium and there are a few things that need to be fixed or added.

*   The CoreCiliumEndpoint struct must include a new string field PodUID (JSON tag: pod-uid, omitempty) that stores the UID of the Pod that owns the endpoint.

*   ConvertCEPToCoreCEP must extract the Pod UID from the CiliumEndpoint's OwnerReferences by finding the first entry with Kind=="Pod" and setting PodUID to the string form of its UID. When no Pod owner reference exists, PodUID must be empty string.

*   ConvertCoreCiliumEndpointToTypesCiliumEndpoint must reconstruct OwnerReferences when PodUID is non-empty: the result's ObjectMeta.OwnerReferences must contain exactly one entry with Kind="Pod" and UID equal to k8sTypes.UID(ccep.PodUID). When PodUID is empty, OwnerReferences must be nil.

*   Four new ztunnel connectivity scenario functions must be added in the cilium-cli connectivity tests package: ZTunnelEnrolledToUnenrolledSameNode, ZTunnelEnrolledToUnenrolledDifferentNode, ZTunnelUnenrolledToEnrolledSameNode, ZTunnelUnenrolledToEnrolledDifferentNode. Each must use expectEncryption=false and sameNamespace=false, with the correct client/server enrollment and node location configuration matching its name.

*   The CiliumEndpointsWatcher interface must be defined in the xds package with two methods: GetCiliumEndpointResource() returning resource.Resource[*types.CiliumEndpoint], and GetCiliumEndpointSliceResource() returning resource.Resource[*v2alpha1.CiliumEndpointSlice].

*   The EndpointEventSource interface's SubscribeToEndpointEvents method signature must change from accepting a *sync.WaitGroup to accepting a chan<- EndpointEventCollection. The ListAllEndpoints method must be removed from the interface.

*   EndpointSource.handleCESUpsert must return EndpointEventCollection instead of emitting events to a channel directly. Updated endpoints must be returned as CREATE events, not as a separate update type.

*   EndpointSource.handleCESDelete must return EndpointEventCollection instead of emitting events to a channel directly. The returned collection must contain REMOVED events for all endpoints in the CES, and the cache entry for the key must be deleted.

*   EndpointSource.SubscribeToEndpointEvents must buffer all events that arrive before the Sync event into an initial EndpointEventCollection and send it on syncCh. After the Sync event, subsequent events must be forwarded individually to sp.endpointRecv. syncCh must be closed when the subscription ends. Events for namespaces not enrolled in ztunnel must be skipped. Nil object events must be skipped.

*   StreamProcessorParams must include two new fields: DB *statedb.DB and EnrolledNamespaceTable statedb.RWTable[*table.EnrolledNamespace]. StreamProcessor must store these as db and enrolledNamespaceTable fields.

*   handleAddressTypeURL must return an error containing the string "unexpected resource names" when both ResourceNamesSubscribe and ResourceNamesUnsubscribe are non-empty. On success it must call SubscribeToEndpointEvents, send an initial empty DeltaDiscoveryResponse with the Address type URL and a non-empty Nonce, and add the nonce to expectedNonce.

*   handleAuthorizationTypeURL must send a DeltaDiscoveryResponse with TypeUrl equal to the authorization type URL constant, empty Resources, empty RemovedResources, and Nonce="0". The nonce "0" must be added to expectedNonce.

*   handleDeltaDiscoveryReq must handle a Nack (expected nonce with ErrorDetail set) by logging the error and returning nil (not an error). When the TypeURL is neither the address type URL nor the authorization type URL, it must return an error containing the string "unexpected type URL".

*   The params struct for NewEnrollmentReconciler must accept three additional fields: EndpointEventChannel chan *xds.EndpointEvent, CiliumEndpointResource resource.Resource[*types.CiliumEndpoint], CiliumEndpointSliceResource resource.Resource[*v2alpha1.CiliumEndpointSlice]. EnrollmentReconciler must store these as endpointEventCh, ciliumEndpointResource, and ciliumEndpointSliceResource.

*   During namespace enrollment (Update), EnrollmentReconciler must emit xds.CREATE events to endpointEventCh for each endpoint found in the namespace via CiliumEndpoint or CiliumEndpointSlice resources (depending on whether CiliumEndpointSlice mode is enabled). During namespace disenrollment (Delete), it must emit xds.REMOVED events for the same endpoints.


*   Interface details: ## Interfaces Required by Tests

---

### pkg/k8s/apis/cilium.io/v2alpha1/types.go

Type: Struct Field
Name: PodUID
Location: pkg/k8s/apis/cilium.io/v2alpha1/types.go
Description: A new string field on the CoreCiliumEndpoint struct, with JSON tag `json:"pod-uid,omitempty"`. Stores the UID of the Pod that owns this endpoint.

---

### pkg/k8s/factory_functions.go

Type: Function
Name: ConvertCEPToCoreCEP
Location: pkg/k8s/factory_functions.go
Signature: ConvertCEPToCoreCEP(cep *cilium_v2.CiliumEndpoint) *cilium_v2alpha1.CoreCiliumEndpoint
Description: Converts a CiliumEndpoint to a CoreCiliumEndpoint. Must search cep.OwnerReferences for an entry with Kind=="Pod" and set the resulting CoreCiliumEndpoint.PodUID to the string form of that OwnerReference's UID. When no Pod owner reference exists, PodUID must be empty string.

Type: Function
Name: ConvertCoreCiliumEndpointToTypesCiliumEndpoint
Location: pkg/k8s/factory_functions.go
Signature: ConvertCoreCiliumEndpointToTypesCiliumEndpoint(ccep *cilium_v2alpha1.CoreCiliumEndpoint, ns string) *types.CiliumEndpoint
Description: Converts a CoreCiliumEndpoint to a types.CiliumEndpoint. When ccep.PodUID is non-empty, must set ObjectMeta.OwnerReferences to a slice with exactly one entry: Kind="Pod", UID=k8sTypes.UID(ccep.PodUID). When ccep.PodUID is empty, OwnerReferences must be nil.

---

### cilium-cli/connectivity/tests/ztunnel.go

Type: Function
Name: ZTunnelEnrolledToUnenrolledSameNode
Location: cilium-cli/connectivity/tests/ztunnel.go
Signature: ZTunnelEnrolledToUnenrolledSameNode() check.Scenario
Description: Returns a scenario that tests plain (unencrypted) traffic from an enrolled pod to an unenrolled pod on the same node. Uses scenarioConfig with name "enrolled-to-unenrolled-same-node", clientEnrollment=enrolled, serverEnrollment=unenrolled, location=sameNode, sameNamespace=false, expectEncryption=false.

Type: Function
Name: ZTunnelEnrolledToUnenrolledDifferentNode
Location: cilium-cli/connectivity/tests/ztunnel.go
Signature: ZTunnelEnrolledToUnenrolledDifferentNode() check.Scenario
Description: Returns a scenario that tests plain (unencrypted) traffic from an enrolled pod to an unenrolled pod on different nodes. Uses scenarioConfig with name "enrolled-to-unenrolled-different-node", clientEnrollment=enrolled, serverEnrollment=unenrolled, location=differentNode, sameNamespace=false, expectEncryption=false.

Type: Function
Name: ZTunnelUnenrolledToEnrolledSameNode
Location: cilium-cli/connectivity/tests/ztunnel.go
Signature: ZTunnelUnenrolledToEnrolledSameNode() check.Scenario
Description: Returns a scenario that tests plain (unencrypted) traffic from an unenrolled pod to an enrolled pod on the same node. Uses scenarioConfig with name "unenrolled-to-enrolled-same-node", clientEnrollment=unenrolled, serverEnrollment=enrolled, location=sameNode, sameNamespace=false, expectEncryption=false.

Type: Function
Name: ZTunnelUnenrolledToEnrolledDifferentNode
Location: cilium-cli/connectivity/tests/ztunnel.go
Signature: ZTunnelUnenrolledToEnrolledDifferentNode() check.Scenario
Description: Returns a scenario that tests plain (unencrypted) traffic from an unenrolled pod to an enrolled pod on different nodes. Uses scenarioConfig with name "unenrolled-to-enrolled-different-node", clientEnrollment=unenrolled, serverEnrollment=enrolled, location=differentNode, sameNamespace=false, expectEncryption=false.

---

### pkg/ztunnel/xds/stream_processor.go

Type: Interface
Name: CiliumEndpointsWatcher
Location: pkg/ztunnel/xds/stream_processor.go
Description: Interface for watching CiliumEndpoints and CiliumEndpointSlices. Enables mocking in tests.
Signature:
  GetCiliumEndpointResource() resource.Resource[*types.CiliumEndpoint]
  GetCiliumEndpointSliceResource() resource.Resource[*v2alpha1.CiliumEndpointSlice]

Type: Interface
Name: EndpointEventSource
Location: pkg/ztunnel/xds/stream_processor.go
Description: Interface for endpoint event sources. The SubscribeToEndpointEvents method is changed: the old wg *sync.WaitGroup parameter is replaced by syncCh chan<- EndpointEventCollection. ListAllEndpoints method is removed. SubscribeToEndpointEvents now buffers pre-Sync events into the EndpointEventCollection sent on syncCh, and closes syncCh when done. Post-Sync events are forwarded directly to the StreamProcessor's endpointRecv channel.
Signature:
  SubscribeToEndpointEvents(ctx context.Context, syncCh chan<- EndpointEventCollection)

Type: Struct
Name: EndpointSource
Location: pkg/ztunnel/xds/stream_processor.go
Description: Provides endpoint data for the XDS server. The k8sCiliumEndpointsWatcher field type must be CiliumEndpointsWatcher (interface), not *watchers.K8sCiliumEndpointsWatcher.
Fields:
  k8sCiliumEndpointsWatcher CiliumEndpointsWatcher
  sp *StreamProcessor

Type: Method
Name: handleCESUpsert
Location: pkg/ztunnel/xds/stream_processor.go
Signature: (es *EndpointSource) handleCESUpsert(ces *v2alpha1.CiliumEndpointSlice, cesCache map[resource.Key]map[string]*types.CiliumEndpoint, key resource.Key) EndpointEventCollection
Description: Processes a CiliumEndpointSlice upsert event. Must return an EndpointEventCollection (not emit to a channel). Updated endpoints are emitted as CREATE events, not as a separate updated type.

Type: Method
Name: handleCESDelete
Location: pkg/ztunnel/xds/stream_processor.go
Signature: (es *EndpointSource) handleCESDelete(ces *v2alpha1.CiliumEndpointSlice, cesCache map[resource.Key]map[string]*types.CiliumEndpoint, key resource.Key) EndpointEventCollection
Description: Processes a CiliumEndpointSlice delete event. Must return an EndpointEventCollection containing REMOVED events for all endpoints, and delete the cache entry.

Type: Struct
Name: StreamProcessor
Location: pkg/ztunnel/xds/stream_processor.go
Description: Handles a single XDS stream. Gains two new fields: db *statedb.DB and enrolledNamespaceTable statedb.RWTable[*table.EnrolledNamespace].

Type: Struct
Name: StreamProcessorParams
Location: pkg/ztunnel/xds/stream_processor.go
Description: Parameters for constructing a StreamProcessor. Gains two new fields: DB *statedb.DB and EnrolledNamespaceTable statedb.RWTable[*table.EnrolledNamespace].

Type: Method
Name: handleAddressTypeURL
Location: pkg/ztunnel/xds/stream_processor.go
Signature: (sp *StreamProcessor) handleAddressTypeURL(req *v3.DeltaDiscoveryRequest) error
Description: Handles the xDS Address type URL request. Returns an error containing "unexpected resource names" when both ResourceNamesSubscribe and ResourceNamesUnsubscribe are non-empty. On success, calls SubscribeToEndpointEvents and sends an initial empty DeltaDiscoveryResponse with the Address type URL. The response has no Resources, no RemovedResources, and a non-empty Nonce that is tracked in expectedNonce.

Type: Method
Name: handleAuthorizationTypeURL
Location: pkg/ztunnel/xds/stream_processor.go
Signature: (sp *StreamProcessor) handleAuthorizationTypeURL(req *v3.DeltaDiscoveryRequest) error
Description: Handles the xDS Authorization type URL request. Sends a DeltaDiscoveryResponse with TypeUrl=xdsTypeURLAuthorization, empty Resources, empty RemovedResources, and Nonce="0". Adds the nonce "0" to expectedNonce.

Type: Method
Name: handleDeltaDiscoveryReq
Location: pkg/ztunnel/xds/stream_processor.go
Signature: (sp *StreamProcessor) handleDeltaDiscoveryReq(req *v3.DeltaDiscoveryRequest) error
Description: Handles an incoming DeltaDiscoveryRequest. When the nonce is expected and ErrorDetail is set (Nack), logs the error but returns nil (does not return an error). When the TypeURL is neither the address type URL nor the authorization type URL, returns an error containing "unexpected type URL".

---

### pkg/ztunnel/reconciler/reconciler.go

Type: Struct
Name: params
Location: pkg/ztunnel/reconciler/reconciler.go
Description: Dependency injection parameters for NewEnrollmentReconciler. Gains three new fields.
New fields:
  EndpointEventChannel        chan *xds.EndpointEvent
  CiliumEndpointResource      resource.Resource[*types.CiliumEndpoint]
  CiliumEndpointSliceResource resource.Resource[*v2alpha1.CiliumEndpointSlice]

Type: Struct
Name: EnrollmentReconciler
Location: pkg/ztunnel/reconciler/reconciler.go
Description: Reconciles namespace enrollment with endpoint enrollment. Gains three new unexported fields accessible from within the same package.
New fields:
  endpointEventCh             chan *xds.EndpointEvent
  ciliumEndpointResource      resource.Resource[*types.CiliumEndpoint]
  ciliumEndpointSliceResource resource.Resource[*v2alpha1.CiliumEndpointSlice]


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.