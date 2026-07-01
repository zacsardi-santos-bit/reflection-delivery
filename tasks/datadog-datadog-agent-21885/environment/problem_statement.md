## Description

The Kubernetes compliance loader that collects security configuration from cluster nodes currently supports self-managed clusters and AWS EKS, but does not recognize Google Kubernetes Engine or Azure Kubernetes Service as managed environments. When the compliance agent runs on a GKE or AKS node, it fails to identify the managed environment and therefore cannot correctly scope or classify the kubelet configuration it collects.

## Expected Behavior

- When running on a GKE node, the loader should detect the managed environment and label it as a GKE environment. GKE configures the kubelet through a dedicated config file, so fields like anonymous auth, read-only port, client CA, and authorization mode should be read from that file's content rather than from command-line arguments.
- Within a GKE config file, certificate file references should be resolved to objects that include file metadata (owner, group, permissions), not left as plain paths.
- When running on an AKS node, the loader should detect the managed environment and label it as an AKS environment. AKS configures the kubelet entirely via command-line flags, so all kubelet security settings should be extracted directly from those flags and exposed on the kubelet configuration object.
- In both cases, control plane components (API server, etcd, controller manager, scheduler, proxy) should not be reported, since managed clusters do not expose those on worker nodes.

## Why This Matters

Without this support, compliance checks run on GKE and AKS worker nodes produce incomplete or incorrectly classified results. Security teams that use this compliance data to audit Kubernetes configurations cannot rely on it for two of the most popular managed Kubernetes platforms.
