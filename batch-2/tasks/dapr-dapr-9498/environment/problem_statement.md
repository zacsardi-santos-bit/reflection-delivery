## Description

When deploying Dapr in high-availability mode on Kubernetes, both the placement and scheduler services should spread their pods across different topology zones to ensure genuine availability. Currently, the placement service's Helm chart does not support a configurable pod anti-affinity policy — it either applies a fixed soft preference or no anti-affinity at all. Additionally, the placement service ignores local high-availability settings when deciding whether to apply pod anti-affinity rules, meaning deployments that enable HA locally for placement but not globally may not get the expected scheduling behavior.

## Expected Behavior

- When high-availability mode is enabled (either globally or locally for the placement service), the placement StatefulSet should include pod anti-affinity rules that spread pods across availability zones.
- Operators should be able to choose between a "preferred" (soft) policy — which tries to spread pods across zones but allows scheduling on the same zone if no other option is available — and a "required" (hard) policy — which enforces that pods must be on different zones and will leave pods unscheduled if the constraint cannot be met.
- The topology key used for zone-based spreading should be configurable.
- The scheduler StatefulSet should also support the same configurable policy (preferred vs. required), since it currently only supports the preferred mode.
- When high-availability mode is disabled, no pod anti-affinity rules should be applied to the placement StatefulSet.

## Why This Matters

Soft pod anti-affinity preferences are not always sufficient for highly available workloads. In production environments, operators often need hard guarantees that critical services like the placement and scheduler servers are distributed across failure domains. Without a way to configure a required policy, there is no guarantee that pods will not all end up on the same zone or node, defeating the purpose of running in HA mode.
