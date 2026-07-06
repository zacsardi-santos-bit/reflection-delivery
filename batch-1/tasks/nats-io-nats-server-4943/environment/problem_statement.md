## Description

When running a NATS gateway cluster with certificate-based stapling enforcement enabled and then rotating the TLS certificates on one of the gateway nodes via a configuration reload, the cluster loses connectivity. After the reload completes, the cluster nodes fail to fully re-establish their outbound connections to the reloaded node, leaving the cluster partially disconnected. Cross-node message delivery breaks as a result.

## Expected Behavior

- When a gateway node's TLS certificates are rotated by updating the configuration and triggering a reload, the certificate verification process should restart and pick up the new certificates.
- After the reload, all nodes in the cluster should successfully re-establish the expected number of outbound gateway connections.
- Clients on any cluster node should be able to send and receive messages to clients on any other node after the reload completes.

## Root Cause

The certificate verification process is only restarted during a reload when top-level (listener) TLS settings change. Changes to gateway-specific TLS certificates do not trigger the same restart path, so the verification process keeps the old certificate state and the cluster fails to reconnect properly with the new certificates.

## Why This Matters

This makes certificate rotation unreliable in production. Any cluster running with stapling-enforced TLS verification cannot safely rotate gateway certificates without causing a connectivity outage that does not self-heal after a reload.
