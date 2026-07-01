I'm running a NATS cluster with multiple gateway nodes, and I have certificate stapling enforcement turned on for TLS verification. When I rotate the TLS certificates on one of the gateway nodes by updating its configuration file and triggering a reload, the cluster doesn't fully recover — only some of the expected gateway connections come back up, and cross-cluster messaging breaks.

It looks like the certificate verification process only restarts when top-level listener TLS settings change during a reload, but it doesn't restart when just the gateway-specific TLS certificates are updated. So after the reload, the verification process is still using the old certificate state and the cluster can't reconnect properly with the new certs.

I'd expect that any time a reload happens, the certificate verification process should be restarted unconditionally so that all nodes can re-establish their connections regardless of which part of the TLS configuration changed.
