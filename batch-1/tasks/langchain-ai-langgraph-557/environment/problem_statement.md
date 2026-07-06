## Description

When building agent loops that call multiple tools in parallel, there is currently no way for a conditional edge to fan out and invoke the same node multiple times with different arguments in a single step. If a model returns three tool calls at once, the developer has to choose a single destination — there is no mechanism to dispatch each tool call individually to a handler node.

We need a way for a conditional edge function to return a collection of directed "packets," where each packet specifies a target node name and the argument to pass to that node. This would allow the same node to be called once per tool call, all within a single graph step.

## Expected Behavior

- A conditional edge can return a list of routing packets, each containing a target node name and its argument.
- Each packet causes the target node to be invoked with that specific argument as its input.
- When multiple packets target the same node, streaming output should group those results as a list under the node name. Normal (non-packet) node output should remain a single dict.
- When multiple packets are pending for a node, the graph's state snapshot should reflect multiple pending next-steps for that node.

## Validation / Error Handling

- If a node returns state keys that do not match the declared state schema (e.g., a typo in a key name), the system should immediately raise a clear validation error rather than silently discarding the invalid data. Currently this failure is silent, which makes debugging difficult.

## Why This Matters

Agent workflows with language models frequently need to process multiple tool calls returned in a single model response. Handling each call independently and in parallel is important for correctness and efficiency. Without this fan-out capability, developers are forced into awkward workarounds that make the code harder to read and maintain.
