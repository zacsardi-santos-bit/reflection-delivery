## Description

When evaluating agentic AI systems, developers need to go beyond checking just the final output — they need to verify the entire sequence of actions the agent took along the way. Currently, promptfoo has no built-in assertion types for validating an agent's execution trajectory (the sequence of tool calls, command executions, searches, reasoning steps, etc. recorded in distributed traces). This makes it impossible to write automated evaluations that check whether an agent used the right tools, called them in the correct order, stayed within expected step counts, or actually accomplished its stated goal.

## Expected Behavior

- Developers should be able to assert that a specific tool was used (or not used) during an agent run, with support for glob pattern matching and count-based constraints.
- Developers should be able to assert that tools were called in a particular order, either as a loose subsequence or as an exact sequence.
- Developers should be able to count steps of a particular type or matching a pattern, and assert that the count falls within expected bounds.
- Developers should be able to assert whether an agent achieved a stated goal, using an LLM judge that receives the full trajectory summary and the agent's final output.
- Trajectory data from distributed traces should be normalized into typed steps (tool calls, command executions, searches, reasoning steps, and generic spans) before being used in assertions.
- Long trajectories should be compacted and summarized for use in LLM-based grading.

## Why This Matters

Agentic evaluations require understanding how an agent behaved, not just what it returned. Without trajectory assertions, teams cannot catch regressions where an agent produces the right answer but via a broken or inefficient path. These new assertion types close that gap and make it practical to write rigorous, automated evaluations for multi-step agent behavior.
