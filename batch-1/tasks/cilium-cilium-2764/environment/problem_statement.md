## Description

When pushing configuration updates to proxy nodes via the discovery protocol, there is currently no way to know when a specific node has actually received and applied an update. The system can send a resource update, but callers have no mechanism to be notified when a node confirms (acknowledges) that it has applied the new version.

This makes it difficult to coordinate operations that depend on configuration being live — for example, you might want to wait for a policy update to be acknowledged by all relevant nodes before proceeding with a traffic switch.

## Expected Behavior

- Callers should be able to attach a completion callback when inserting, updating, or deleting a resource. The callback should fire once all specified proxy nodes have acknowledged the relevant version.
- For resource insertions and updates, the acknowledgment should match both the correct version and the correct resource name per node.
- For resource deletions, the acknowledgment should match by version only (since the deleted resource won't appear in the node's acknowledgment by name).
- A more recent acknowledgment (higher version number) should also satisfy a pending completion registered for an older version.
- When multiple nodes are specified, the completion should only fire once every specified node has acknowledged.
- The server configuration should associate an acknowledgment observer alongside the resource source for each supported resource type, so that acknowledgments arriving via the discovery protocol stream can be routed to the appropriate observer.
- Node identifiers in the Istio proxy format should be parseable to extract the node's IP address, which serves as the node's canonical identifier for acknowledgment tracking. Malformed or missing node identifiers should produce an error.

## Why This Matters

Without this mechanism, there is no reliable way to confirm that a configuration update has been applied end-to-end. Adding acknowledgment-based completion tracking allows higher-level systems to safely gate further actions on confirmed propagation of configuration to the relevant proxy nodes.
