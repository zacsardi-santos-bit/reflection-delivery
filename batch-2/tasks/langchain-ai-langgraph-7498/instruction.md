I'm building a graph-based workflow with human-in-the-loop interrupts, and I'm running into a serious problem with time travel.

*   When invoking a graph with a past checkpoint config (time travel / replay), a fork checkpoint must be created automatically before new execution begins. The checkpoint history must grow by 2 entries (one fork plus the new execution's checkpoints) rather than by 1.

*   The fork checkpoint must carry metadata with the key 'source' set to the string 'fork'. This distinguishes it from regular execution checkpoints (source='loop'), initial checkpoints (source='input'), and state-update checkpoints (source='update').

*   The fork checkpoint must become the most recent entry in the checkpoint history (index 0 in newest-first ordering returned by get_state_history / aget_state_history) immediately after the time-travel invocation.

*   The fork checkpoint's parent_config['configurable']['checkpoint_id'] must equal the checkpoint ID of the original replay point — the checkpoint from which execution was re-started.

*   A fork checkpoint must NOT be created when the loaded checkpoint already has metadata['source'] equal to 'update' or 'fork', since those already represent a fork branch.

*   When replaying multiple times from the same checkpoint before an interrupt, each replay produces a fork with a unique interrupt ID. Across replays the interrupt value (the question posed to the user) and the graph state values must remain equal, but full result equality is not required because interrupt IDs differ.

*   When invoking from a subgraph-level checkpoint config (time travel into a subgraph interrupt), the fork checkpoint must be created in the parent graph's history. The fork's parent must correspond to the parent-level checkpoint at the time of the subgraph's original execution; specifically, fork.parent_config['configurable']['checkpoint_id'] must equal the checkpoint_map root key from the subgraph config.

*   After time travel, fork creation, and resume with new inputs: nodes that precede the replay point must not be re-executed; only the node(s) at and after the replay point are run within the new branch.

*   After time travel + fork + resume, the original execution branch's checkpoints must be preserved intact in history. The new execution branch's checkpoints are prepended (appear at lower indexes in newest-first order), with the fork checkpoint as the root of the new branch.

*   When a subgraph uses an inherited/stateful checkpointer and its parent is replaying from a past checkpoint, the subgraph must load accumulated state from the end of prior invocations (not start fresh), so the starting state visible to the subgraph's nodes reflects previously accumulated values.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.