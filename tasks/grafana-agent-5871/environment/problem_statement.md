## Description

The Grafana Agent Helm chart currently has no mechanism for deploying additional sidecar containers alongside the main agent and config-reloader containers within the same pod. This limits users who need to run companion workloads — for example, a service that continuously downloads and refreshes a database that the agent reads from — because there is no supported way to co-locate such containers and share data between them through Helm values alone.

## Expected Behavior

- Users should be able to define one or more extra containers in Helm values, and those containers should appear in the rendered DaemonSet (or other controller) alongside the existing agent and config-reloader containers.
- Extra volumes declared alongside a sidecar should be added to the pod-level volumes list so the sidecar can mount them.
- Extra volume mounts for the main agent container should also be configurable, allowing the agent to access data produced by the sidecar.
- A CI test values file demonstrating this configuration (using a GeoIP updater sidecar pattern) should be included to validate the feature end-to-end.

## Why This Matters

Without this capability, users wanting to extend the agent pod with helper containers must either maintain a fork of the chart or use post-render hooks, both of which add operational overhead. Supporting extra containers directly in values.yaml enables common patterns — such as continuously updated geographic enrichment databases — to be configured declaratively and tested as part of the chart's standard CI process.
