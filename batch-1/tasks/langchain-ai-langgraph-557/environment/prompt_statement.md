I'm building an agent workflow where a language model can return multiple tool calls in a single response, and I need each tool call to be dispatched individually to the same handler node in parallel. Right now, the only thing I can return from a conditional edge is a string destination (or a signal to stop the graph), so I can't fan out to the same node multiple times with different arguments in a single step.

I'd like to introduce a way to return a list of "packets" from a conditional edge function — each packet carrying a target node name and the specific argument to pass to that node — so the graph can invoke the same node once per packet within a single step. When streaming, the results from packet-invoked nodes should appear grouped as a list under the node name, while normally-scheduled nodes should still produce their output as a single dict.

There's also a related issue: if a node accidentally returns a key that doesn't exist in the declared state schema (like a typo), the system currently silently drops the data instead of raising an error. I'd like it to immediately raise a clear validation error in that case so the bug is easy to catch.

Additionally, when I call the graph's state-update functionality with a message that has the same ID as an existing message, I'd like the existing message to be replaced rather than a new copy being appended.
