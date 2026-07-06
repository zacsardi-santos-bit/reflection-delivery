## Description

When a user travels back to a past execution point (time travel / replay) in a graph that uses human-in-the-loop interrupts, resuming with new inputs after the replay does not work correctly. Instead of building a new execution branch from the point of divergence, the graph resumes on the old branch — picking up stale state rather than the fresh state established by the replay.

This is particularly broken in graphs that contain subgraphs with their own checkpointers. When you time-travel to a subgraph checkpoint where an interrupt fired and then resume with a new answer, the graph may find the wrong checkpoint for the subgraph's state, causing it to behave as if the old answer was still in effect.

## Expected Behavior

- Traveling back to a past checkpoint and re-invoking the graph should automatically create a branching marker in the checkpoint history, so that the new execution clearly diverges from the old one.
- The branching marker must be recorded as the newest entry in the history and must link back to the original divergence point.
- The original execution branch's checkpoints must be preserved unchanged.
- Resuming after the branch point must execute along the new branch, not the old one.
- When time-traveling into a subgraph's interrupt checkpoint, the branch marker must appear in the parent graph's history with the correct lineage.
- Replaying the same checkpoint multiple times (for example, to re-fire the same interrupt question) must yield consistent interrupt values and state values across replays.

## Why This Matters

Without this fix, any workflow that uses "redo from this point" or "try a different answer" via time travel is unreliable. Resuming after a time-travel can silently pull the wrong prior state, leading to incorrect graph outputs. This makes time travel and multi-turn human-in-the-loop workflows fundamentally broken for users who need to explore alternate execution paths.
