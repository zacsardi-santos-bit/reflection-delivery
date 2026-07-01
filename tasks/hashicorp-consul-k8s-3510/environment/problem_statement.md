## Description

The injected proxy sidecar containers and network-setup init containers in the Consul service mesh are not configured with all recommended security hardening settings. Specifically, these containers are missing an explicit restriction on privilege escalation, which means a process inside the container could potentially gain additional privileges at runtime. The init container responsible for setting up network traffic redirection is also missing a read-only root filesystem restriction.

## Expected Behavior

- The sidecar proxy container injected into each pod should have its security context configured to explicitly prevent privilege escalation, in addition to the existing restrictions (non-root user/group, read-only root filesystem).
- The init container used when CNI-based network setup is active (and transparent proxy is not separately enabled) should have its security context set to both prevent privilege escalation and enforce a read-only root filesystem, in addition to dropping all Linux capabilities.
- These security settings should apply consistently across both standard and v2 webhook injection paths.

## Why This Matters

Failing to explicitly disable privilege escalation in injected containers is a security gap that violates the principle of least privilege. Kubernetes security policies and compliance tools often flag containers that do not set this restriction. Hardening these injected containers by default reduces attack surface and brings the inject behavior in line with security best practices.
