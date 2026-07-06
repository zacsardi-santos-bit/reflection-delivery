I'm working on the Kubernetes compliance module in the Datadog Agent. Right now it can detect and load kubelet configuration for self-managed clusters and AWS EKS nodes, but it has no support for Google Kubernetes Engine or Azure Kubernetes Service. I need it to recognize those two environments and correctly extract the kubelet security settings for each.

For GKE, the kubelet is configured through a config file rather than command-line flags, so fields like anonymous auth, read-only port, client CA, and authorization mode should come from that file's contents rather than the process arguments. Certificate file references in the config file should also be resolved to objects that carry file metadata (ownership and permissions), not left as raw string paths.

For AKS, the kubelet is configured entirely through command-line flags, so all the relevant settings — anonymous auth, read-only port, event QPS, max pods, certificate rotation, authorization mode, CA file, TLS cert and key files, TLS cipher suites, and feature gates — should be parsed from those flags and exposed on the kubelet configuration object.

In both cases, the managed environment should be identified by the right name ("gke" or "aks"), control plane components should be absent (since managed platforms don't expose them on worker nodes), and the result should have no errors.
