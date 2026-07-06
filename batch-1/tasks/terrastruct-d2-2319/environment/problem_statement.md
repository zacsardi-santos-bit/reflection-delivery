## Description

Deleting a connection using the absolute syntax or parent-scoped syntax does not actually remove the connection when the nodes involved have multi-level paths.

For example, if you define the same connection between two deeply nested nodes twice and then try to delete each occurrence by using an indexed deletion assignment on it — using either the full absolute path form or the parent-node-scoped form — the connections are not removed. The resulting diagram still shows both connections even though both were explicitly deleted.

## Expected Behavior

- Deleting a connection by index using the full absolute path for both source and destination should remove that connection from the compiled output.
- Deleting a connection by index using the parent-scoped syntax (where the connection is expressed relative to a parent node) should also remove that connection.
- After both connections are removed, the compiled graph should have zero connections remaining, while the referenced nodes still exist.

## Why This Matters

Users who want to conditionally remove connections in their diagrams — for example in scenarios or overlays — rely on the indexed connection deletion feature. When this feature silently fails for deeply nested connections, diagrams cannot be built as intended and the deletion syntax becomes unreliable for these common cases.
