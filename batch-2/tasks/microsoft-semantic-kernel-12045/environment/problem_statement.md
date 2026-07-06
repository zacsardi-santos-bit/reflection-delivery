## Description

The multi-agent orchestration framework supports concurrent and sequential agent patterns, but there is no built-in group chat pattern where multiple agents can take turns in a structured conversation controlled by a coordinator. Teams working on collaborative agent workflows need a way to run a round-table discussion among agents, where a manager decides when to stop, selects which agent speaks next, and extracts a final result from the conversation history.

## Expected Behavior

- A new group chat orchestration type should allow multiple agents to participate in a back-and-forth conversation managed by a configurable group chat manager.
- All participating agents must have a description set; attempting to create the orchestration without descriptions should be rejected with a clear error.
- A default round-robin manager should be provided that cycles through agents in the order they are listed, stopping after a configurable maximum number of rounds.
- The manager should track how many rounds have occurred and report whether the conversation should terminate.
- When given a list of initial messages, the orchestration should incorporate them into each agent's context before invoking the agents.
- An optional callback should allow callers to observe each agent response in real time as the conversation progresses.
- The caller should be able to cancel a running orchestration mid-flight, but attempting to cancel after the orchestration has already completed should raise an error.

## Why This Matters

Many multi-agent use cases—such as iterative content refinement, structured debates, or multi-perspective reviews—require agents to build on each other's responses over several rounds. Without a group chat pattern, developers must wire up this logic manually. A built-in orchestration type with a configurable manager reduces boilerplate and makes collaborative agent workflows easy to compose and extend.
