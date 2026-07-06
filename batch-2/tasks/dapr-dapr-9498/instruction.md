I'm working on the Dapr control plane Helm charts and I need to add configurable pod anti-affinity support to the placement and scheduler services.

*   A new function WithShowOnlyPlacementSTS() must be added to tests/integration/framework/process/helm/options.go. It must return an OptionFunc that appends the string "charts/dapr_placement/templates/dapr_placement_statefulset.yaml" to the options showOnly slice.

*   The global Helm values must include a new field global.ha.podAntiAffinityPolicy with default value "preferredDuringSchedulingIgnoredDuringExecution". The valid values are "preferredDuringSchedulingIgnoredDuringExecution" and "requiredDuringSchedulingIgnoredDuringExecution".

*   The placement StatefulSet Helm template must render a podAntiAffinity block when HA is active. HA is active when either global.ha.enabled is true OR the local dapr_placement.ha value is true.

*   When HA is active for placement and global.ha.podAntiAffinityPolicy is "preferredDuringSchedulingIgnoredDuringExecution" (the default), the placement StatefulSet must render a preferredDuringSchedulingIgnoredDuringExecution entry with weight 100, topologyKey equal to global.ha.topologyKey (default "topology.kubernetes.io/zone"), and a labelSelector with a matchExpression having key "app", operator "In", and values ["dapr-placement-server"]. The requiredDuringSchedulingIgnoredDuringExecution list must be absent.

*   When HA is active for placement and global.ha.podAntiAffinityPolicy is "requiredDuringSchedulingIgnoredDuringExecution", the placement StatefulSet must render a requiredDuringSchedulingIgnoredDuringExecution entry with topologyKey equal to global.ha.topologyKey and a labelSelector with a matchExpression having key "app", operator "In", and values ["dapr-placement-server"]. The preferredDuringSchedulingIgnoredDuringExecution list must be absent.

*   When HA is disabled for placement (neither global.ha.enabled nor dapr_placement.ha is true), the placement StatefulSet must not render any podAntiAffinity configuration (the affinity.podAntiAffinity field must be nil/absent).

*   The scheduler StatefulSet Helm template must always render a podAntiAffinity block. When global.ha.podAntiAffinityPolicy is "preferredDuringSchedulingIgnoredDuringExecution" (the default), it must render preferredDuringSchedulingIgnoredDuringExecution with weight 100, topologyKey equal to global.ha.topologyKey (default "topology.kubernetes.io/zone"), and a labelSelector with matchExpression having key "app", operator "In", and values ["dapr-scheduler-server"]. The requiredDuringSchedulingIgnoredDuringExecution list must be absent.

*   When global.ha.podAntiAffinityPolicy is "requiredDuringSchedulingIgnoredDuringExecution", the scheduler StatefulSet must render requiredDuringSchedulingIgnoredDuringExecution with topologyKey equal to global.ha.topologyKey and a labelSelector with matchExpression having key "app", operator "In", and values ["dapr-scheduler-server"]. The preferredDuringSchedulingIgnoredDuringExecution list must be absent.

*   Both the placement and scheduler StatefulSet templates must use global.ha.topologyKey as the topology key for pod anti-affinity terms. When global.ha.topologyKey is set to a custom value (e.g. "kubernetes.io/hostname"), that custom value must appear as the topologyKey in the rendered podAffinityTerm.


*   Interface details: Type: Function
Name: WithShowOnlyPlacementSTS
Location: tests/integration/framework/process/helm/options.go
Signature: WithShowOnlyPlacementSTS() OptionFunc
Description: Returns an OptionFunc that appends "charts/dapr_placement/templates/dapr_placement_statefulset.yaml" to the helm options showOnly list, filtering helm template output to show only the placement StatefulSet.

---

## Helm Chart Configuration Requirements

The following Helm chart files must be modified to support the new pod anti-affinity policy configuration:

### charts/dapr/values.yaml
Must add `global.ha.podAntiAffinityPolicy` with default value `preferredDuringSchedulingIgnoredDuringExecution`.

### charts/dapr/charts/dapr_placement/templates/dapr_placement_statefulset.yaml
The pod anti-affinity block must:
- Be rendered when either `global.ha.enabled` is true OR `dapr_placement.ha` is true (local override)
- When `global.ha.podAntiAffinityPolicy` equals `requiredDuringSchedulingIgnoredDuringExecution`: render `requiredDuringSchedulingIgnoredDuringExecution` with topologyKey from `global.ha.topologyKey` and labelSelector matchExpression key=`app`, operator=In, values=[`dapr-placement-server`]
- Otherwise (default preferred): render `preferredDuringSchedulingIgnoredDuringExecution` with weight=100, podAffinityTerm topologyKey from `global.ha.topologyKey`, labelSelector matchExpression key=`app`, operator=In, values=[`dapr-placement-server`]
- Be absent entirely when HA is not enabled

### charts/dapr/charts/dapr_scheduler/templates/dapr_scheduler_statefulset.yaml
The pod anti-affinity block must:
- When `global.ha.podAntiAffinityPolicy` equals `requiredDuringSchedulingIgnoredDuringExecution`: render `requiredDuringSchedulingIgnoredDuringExecution` with topologyKey from `global.ha.topologyKey` and labelSelector matchExpression key=`app`, operator=In, values=[`dapr-scheduler-server`]
- Otherwise (default preferred): render `preferredDuringSchedulingIgnoredDuringExecution` with weight=100, podAffinityTerm topologyKey from `global.ha.topologyKey`, labelSelector matchExpression key=`app`, operator=In, values=[`dapr-scheduler-server`]


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.