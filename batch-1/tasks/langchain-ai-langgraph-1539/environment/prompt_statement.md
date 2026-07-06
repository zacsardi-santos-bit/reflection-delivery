I'm working with a graph framework that supports nested subgraphs and human-in-the-loop interrupts, and I'm running into several related issues.

First, when my outer graph pauses because a subgraph node is waiting at an interrupt, I can see the pending tasks in the state snapshot, but those tasks don't carry any direct reference to where the subgraph's state actually lives. I have to manually build checkpoint references to look inside the subgraph. It would be much cleaner if each pending task just told me how to reach its subgraph's state directly, and if I could pass that reference back to the normal state inspection and update methods.

Second, when I enable subgraph expansion while inspecting state, I'd expect each task's state to be fully expanded into a complete nested state object — not just a pointer. This would let me see the full picture of what's happening inside a paused subgraph without making additional calls.

Third, the streaming behavior is off when resuming from an interrupt. If I'm streaming in value mode, the current state isn't included at the start of the resumed stream — I only see the states that result from nodes running after the resume. Similarly, the node output that completed just before the interrupt isn't re-emitted during the resumed stream. And in the async case, resuming a graph after an interrupt returns nothing instead of the final state.

Finally, I'd like to be able to fork execution from a historical checkpoint by performing an empty state update and streaming from the resulting config — this should replay execution from that point, including any subgraph operations.

Could you help fix these issues so that subgraph state is properly accessible via task references, state updates work through those references, streaming always shows the complete picture across interrupt boundaries, and forking from historical checkpoints works correctly?
