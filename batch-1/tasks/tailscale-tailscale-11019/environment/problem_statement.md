## Description

The Tailscale Kubernetes operator needs a new controller that automatically manages DNS record mappings for Tailscale proxy services running inside the cluster. Currently, when egress proxy services (those forwarding traffic to a tailnet address via a dedicated annotation) or ingress proxies are deployed, there is no mechanism to keep the cluster's internal nameserver configuration up to date. As a result, the nameserver cannot resolve Tailscale domain names to the correct proxy pod addresses.

## Expected Behavior

- When an egress proxy service has a tailnet FQDN annotation, the operator should automatically create and maintain a DNS record mapping that FQDN to the current IP address(es) of the corresponding proxy pod(s).
- When an ingress proxy is deployed, the operator should automatically create and maintain a DNS record mapping the ingress's MagicDNS hostname to the current IP address(es) of the ingress proxy pod(s).
- DNS records should be updated automatically when the proxy pod IP changes, when the FQDN annotation value changes, or when the ingress hostname changes.
- DNS records should only be maintained when exactly one properly configured and ready nameserver resource exists in the cluster.
- These DNS mappings should be stored in a shared configuration resource in a format that the cluster nameserver can consume.

## Naming Cleanup

As part of this change, the configuration resource used to store DNS records and the field reporting the nameserver's IP in the DNS configuration status have been renamed for clarity and consistency.

## Why This Matters

Without this controller, the cluster's Tailscale nameserver has no way to learn the current IP addresses of the proxy pods it needs to route traffic through. This is essential for proper DNS-based traffic routing within a Tailscale-enabled Kubernetes cluster.
