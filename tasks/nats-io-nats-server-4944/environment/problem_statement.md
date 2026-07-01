## Description

When a NATS server cluster uses certificate validity enforcement on gateway connections and a server reloads its configuration with rotated TLS certificates (only in the gateway section, not the main client listener), the certificate validity monitors are not restarted. This prevents the server from obtaining fresh validity proofs for the new certificates, causing TLS handshakes to fail between gateway nodes.

## Expected Behavior

- When a server reloads its configuration with rotated gateway TLS certificates, certificate validity monitors should always restart — regardless of whether TLS is also configured on the main client listener.
- After the reload and certificate rotation, all nodes in the gateway mesh should re-establish full connectivity with each other.
- Clients connected to any node should be able to exchange messages with clients on other nodes across the gateway mesh after a cert-rotation reload.

## Observed Behavior

Certificate validity monitors are only restarted on reload if TLS is configured at the root/client listener level. If TLS is only used on gateway connections, rotating the gateway certificates through a configuration reload leaves the monitors stale. This breaks inter-cluster gateway connections and cross-gateway messaging because the server cannot prove the validity of its new certificates.

## Why This Matters

Operators need to be able to rotate TLS certificates on gateway connections via a configuration reload without losing cluster connectivity. Currently, performing such a rotation silently breaks gateway-to-gateway TLS and disrupts message routing across the cluster.
