I'm working on improving the handler that syncs the system-upgrade-controller status condition on control plane objects in Rancher.

*   The handler struct must replace its clusterCache and downstreamPlanClient fields with a rkeControlPlaneController field (of type rkecontrollers.RKEControlPlaneController) and a pendingEnqueues field (of type sync.Map).

*   A throttleState struct must be defined in the package with two fields: lastDownstreamCheck (time.Time) and enqueuePending (bool).

*   The Register function must remove the clusterCache and downstreamPlanClient parameters; its new signature must accept only mgmtClusterName, downstreamAppClient, and rkeControlPlaneController.

*   The syncSystemUpgradeControllerCondition handler must check the AgentConnected bool field on the RKEControlPlaneStatus directly to determine cluster connectivity, instead of querying a provisioning cluster cache. When AgentConnected is false, the handler must return early without calling the downstream app API.

*   The handler must implement a per-object throttle: downstream app API calls must be skipped if a call was made within the last 30 seconds (the throttle duration). The throttle state is tracked in the pendingEnqueues sync.Map, keyed by 'namespace/name' of the RKEControlPlane object.

*   When the throttle is active (a recent downstream call exists) and enqueuePending is false, the handler must call rkeControlPlaneController.EnqueueAfter with the remaining wait duration and set enqueuePending to true, then return without calling the downstream API.

*   When the throttle is active and enqueuePending is already true, the handler must skip both the downstream API call and the EnqueueAfter call.

*   When the throttle window has expired (more than 30 seconds since the last downstream check), the handler must record a new throttleState with the current time in pendingEnqueues and then proceed with the downstream API call.

*   When the RKEControlPlane object is being deleted (DeletionTimestamp is set), the handler must delete the object's key from pendingEnqueues before returning.

*   When the downstream app API call returns a transient (non-NotFound) error, the handler must delete the object's key from pendingEnqueues so subsequent retries are not throttled.


*   Interface details: Type: Struct
Name: handler
Location: pkg/controllers/managementuser/rkecontrolplanecondition/rkecontrolplanecondition.go
Description: The handler struct for the RKE control plane condition controller. Must contain the following fields: mgmtClusterName (string), downstreamAppClient (catalogv1.AppClient), rkeControlPlaneController (rkecontrollers.RKEControlPlaneController), and pendingEnqueues (sync.Map). The clusterCache and downstreamPlanClient fields must be removed.

Type: Struct
Name: throttleState
Location: pkg/controllers/managementuser/rkecontrolplanecondition/rkecontrolplanecondition.go
Description: Tracks throttle and enqueue deduplication state for a single RKEControlPlane object. Must contain fields: lastDownstreamCheck (time.Time) recording when the downstream API was last called, and enqueuePending (bool) indicating whether an EnqueueAfter has already been scheduled for the current throttle window.

Type: Function
Name: Register
Location: pkg/controllers/managementuser/rkecontrolplanecondition/rkecontrolplanecondition.go
Signature: Register(ctx context.Context, mgmtClusterName string, downstreamAppClient catalogv1.AppClient, rkeControlPlaneController rkecontrollers.RKEControlPlaneController)
Description: Registers the RKE control plane condition handler. The clusterCache and downstreamPlanClient parameters must be removed from this function signature.

Type: Function
Name: syncSystemUpgradeControllerCondition
Location: pkg/controllers/managementuser/rkecontrolplanecondition/rkecontrolplanecondition.go
Signature: syncSystemUpgradeControllerCondition(obj *rkev1.RKEControlPlane, status rkev1.RKEControlPlaneStatus) (rkev1.RKEControlPlaneStatus, error)
Description: Handles the sync of the system-upgrade-controller status condition on RKEControlPlane objects. Must check status.AgentConnected (a bool field on RKEControlPlaneStatus) instead of performing a cluster cache lookup to determine connectivity. Must implement a per-object throttle via pendingEnqueues sync.Map: skip the downstream app API call if a call was made within the last 30 seconds, calling rkeControlPlaneController.EnqueueAfter when the throttle fires (unless enqueuePending is already true). On object deletion, must delete the object's key from pendingEnqueues. On transient (non-NotFound) errors from the downstream app client, must delete the object's key from pendingEnqueues so retries are not throttled.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.