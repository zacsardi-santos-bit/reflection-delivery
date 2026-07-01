## Description

K8sGPT currently lacks the ability to analyze resources managed by the Operator Lifecycle Manager (OLM) — a widely used system in OpenShift and other Kubernetes distributions that handles the installation, upgrades, and management of in-cluster operators. This means that problems with OLM-managed workloads are invisible to k8sgpt, leaving cluster administrators without actionable diagnostics for a large portion of their cluster's resource landscape.

## Expected Behavior

K8sGPT should be able to detect and report issues with the following OLM resource types:

- **Catalog Sources**: Surface catalog sources that are in an unhealthy connection state (any state other than healthy/ready), including the name of the failed state in the reported error.
- **Cluster Service Versions**: Report operator versions that have not reached the succeeded phase, including condition details in the error message.
- **Install Plans**: Report operator install plans that have not completed successfully, including the failure reason from conditions.
- **Operator Groups**: Detect namespaces that contain more than one operator group, which can cause operator resolution failures. The reported name should be the namespace itself.
- **Subscriptions**: Report operator subscriptions that are not at the latest known state, including condition reasons such as unreachable catalog sources.

Healthy resources (those in the normal/complete/latest state) should be silently ignored and produce no results.

## Why This Matters

Without these analyzers, operators running OLM-managed clusters have no automated way to detect broken operator installations, misconfigured operator groups, or unhealthy catalog sources through k8sgpt. Adding these analyzers brings OLM resource health into the same diagnostic surface that k8sgpt already provides for standard Kubernetes workloads.
