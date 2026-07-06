## Description

The HBase region balancer currently has no built-in mechanism to keep the internal metadata catalog table on dedicated region servers, isolated from user and system tables. When the balancer distributes regions across the cluster, metadata table regions can end up sharing servers with ordinary user table regions, which can negatively affect metadata availability and latency — especially under heavy workloads.

## Expected Behavior

- Operators should be able to enable a configuration flag that instructs the balancer to isolate the metadata table onto its own dedicated region servers.
- When this flag is enabled, the balancer should actively move regions so that no server hosting metadata regions also hosts regions from any other table.
- This isolation feature should work correctly alongside other balancing policies, such as distributing region replicas across distinct servers — both constraints should be satisfiable simultaneously.
- The balancer should expose a way to query whether table isolation is currently enabled, replacing older internal checks that conflated isolation with other balancing concepts.

## Why This Matters

Isolating metadata to dedicated servers is a common operational need for large clusters, where contention on metadata servers can cause widespread latency spikes. This feature gives operators a supported, configuration-driven way to enforce that separation rather than relying on manual region placement.
