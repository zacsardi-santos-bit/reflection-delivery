## Description

When using graphs with nested subgraphs and interrupts, there is no clean way to inspect or modify the state of a paused subgraph. Currently, subgraph states are stored under a separate dictionary field on the state snapshot, making it awkward to navigate into a specific subgraph's state or update it directly. Developers have to manually construct checkpoint namespace references to access subgraph state, which is error-prone and doesn't compose well with the rest of the state management API.

Additionally, the streaming behavior when resuming from an interrupt is incomplete: when using value-mode streaming, the current graph state is not emitted at the start of the resumed stream, and the output from the node that ran just before the interrupt is also missing from the resumed stream. This makes it difficult to track what happened across an interrupt boundary.

## Expected Behavior

- When inspecting the state of a paused graph that has a pending subgraph task, each pending task should carry a direct reference to the subgraph's checkpoint. This reference should work as a config that can be passed directly to state inspection and update methods.
- Inspecting state with subgraph expansion enabled should return full nested state snapshots for each pending task, not just checkpoint references.
- Updating a subgraph's state should be possible by passing the task's state reference as the config, without manually constructing checkpoint namespace paths.
- When resuming a graph from an interrupt using value-mode streaming, the current state should be emitted as the first item in the stream.
- When resuming from an interrupt using update-mode streaming, the already-completed output from just before the interrupt should be included at the start of the stream.
- Resuming a graph asynchronously after an interrupt should return the final computed state, not an empty result.
- It should be possible to fork execution from a historical checkpoint by performing a state update with no new values, then stream from the resulting config to replay from that point.

## Why This Matters

Subgraphs are a powerful compositional tool, but without the ability to easily inspect and modify their internal state during an interrupt, human-in-the-loop patterns become very difficult to implement correctly. Fixing the streaming behavior also ensures that callers always have a complete, consistent picture of graph execution across interrupt boundaries.
