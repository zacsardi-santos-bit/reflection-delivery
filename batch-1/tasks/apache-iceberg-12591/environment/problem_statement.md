## Description

Iceberg's file I/O layer currently has no standard mechanism for receiving dynamically-vended storage credentials from a catalog server. When a REST catalog provides short-lived, path-scoped credentials (for example, temporary access credentials for a specific S3 bucket or an OAuth token for a GCS bucket) in its table response, there is no way to forward those credentials into the file I/O instance that will actually access that storage location.

## Expected Behavior

- A new contract (interface) should be introduced that file I/O implementations can optionally adopt to receive a list of storage credentials before initialization. Each credential pairs a URI prefix with a map of configuration key-value pairs.
- When credentials are provided, they should take precedence over any matching static credentials configured via the regular properties map.
- Storage systems that only support a single credential per type (such as S3 and GCS) should fail clearly if more than one matching credential is supplied.
- The general file I/O loading utility should be updated to accept and forward these credentials to any implementation that supports the new contract.
- A delegating file I/O that routes I/O operations to scheme-specific backends must propagate its credentials to those backends when loading them.
- Credentials must survive both standard Java serialization and a secondary serialization mechanism used in distributed cluster environments, so that file I/O instances work correctly in distributed processing environments.

## Why This Matters

Vended credentials are essential for fine-grained, short-lived access control to storage in catalog-server-managed environments. Without this capability, clients must rely entirely on long-lived static credentials configured at cluster level, which is a security risk and prevents per-table credential isolation.
