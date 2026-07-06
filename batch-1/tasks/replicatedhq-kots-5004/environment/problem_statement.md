## Description

When a new node is being added to an embedded cluster, the join process currently generates a token and command for the node to use, but it does not tell the joining node which network endpoints it needs to be able to reach on existing cluster nodes. This means the joining node cannot validate its network connectivity before attempting to join — resulting in potentially confusing failures mid-join if required ports are unreachable.

We need to include a list of required TCP connections in the join command response, so the joining node can pre-check connectivity before starting the join process.

## Expected Behavior

- The node join command response should include a list of TCP endpoints (host:port pairs) that the joining node must be able to reach.
- The list should be tailored to the role the joining node will have:
  - **Worker nodes** only need to reach the Kubernetes API server port and the cluster overlay API port on each existing controller node.
  - **Controller nodes** additionally need to reach the cluster state replication port and the node communication port on each existing controller node, plus the node communication port on each existing worker node.
- Only nodes that are currently in a healthy/ready state should be included in the list.
- When there are no existing nodes, the endpoint list should be empty.

## Additional Context

The current implementation always uses the real cluster Kubernetes client directly in all handlers, which makes it difficult to write unit tests. The handler should be refactored to use an injectable client abstraction so that tests can supply a fake client. This abstraction should live in its own sub-package and include both a real implementation and a mock implementation for testing.

## Why This Matters

Without this information, operators and automated tooling cannot verify that a node has the necessary network access before a join is attempted. This change enables connectivity pre-checks that can catch firewall or routing issues early, avoiding failed or partial cluster joins.
