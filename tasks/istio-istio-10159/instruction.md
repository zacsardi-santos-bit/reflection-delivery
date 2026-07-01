Implement TCP keepalive support in Istio's cluster generation logic to ensure that keepalive settings are correctly propagated into Envoy cluster configurations. Ensure that global mesh-wide settings are applied unless overridden by specific destination traffic policies, and handle cases where no settings are configured.

*   Ensure that when no TCP keepalive settings are configured in either the global mesh configuration or a DestinationRule traffic policy, the generated outbound cluster's upstream connection options are nil, meaning no keepalive structure is present.
*   Implement logic to apply global mesh-wide TCP keepalive settings:
    *   If a global mesh-wide TCP keepalive time is configured, set the generated outbound cluster's UpstreamConnectionOptions to include a TcpKeepalive entry.
    *   Set KeepaliveTime to the configured seconds value.
    *   Leave KeepaliveProbes and KeepaliveInterval as nil if they are not configured.
*   Override global settings with DestinationRule traffic policy settings:
    *   If a DestinationRule traffic policy specifies a TCP keepalive time for a port, ensure this value overrides the global mesh-wide keepalive setting in the generated cluster's UpstreamConnectionOptions.
    *   If a DestinationRule traffic policy sets an empty TCP keepalive configuration (no time, probes, or interval values), ensure the generated cluster includes UpstreamConnectionOptions with a non-nil TcpKeepalive, but all fields (KeepaliveProbes, KeepaliveTime, KeepaliveInterval) must remain nil to signal OS-level defaults.
*   Ensure that TCP keepalive settings from the MeshConfig are applied to outbound clusters during cluster generation, providing a global control mechanism for mesh operators.
*   Prioritize TCP keepalive settings from a DestinationRule's port-level traffic policy over mesh-wide keepalive settings when both are present.
*   Express the KeepaliveTime value in the cluster's UpstreamConnectionOptions as a uint32 representing the number of seconds, derived from the duration configuration.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.