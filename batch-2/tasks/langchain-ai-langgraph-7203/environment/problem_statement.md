# Add integration identifier to LangGraph execution metadata

## Description

When LangGraph executes a graph, it emits metadata alongside each execution event — tracking which node is running, what step number it is, what triggered it, and so on. However, this metadata currently contains no information identifying that the execution originates from a LangGraph integration.

External observability, tracing, and monitoring tools that consume these execution events cannot automatically identify and categorize them as LangGraph runs. There's no standard marker in the metadata that distinguishes LangGraph traces from other frameworks. This forces consumers of these events to rely on indirect signals or manual configuration.

## Expected Behavior

- All execution events emitted during graph execution should include a standard integration identifier in their metadata
- The identifier should appear at every level: the root graph level, individual node executions, subgraph executions, and error/exception event paths
- Both synchronous and asynchronous execution paths should include the identifier
- The identifier should only be set if not already present (i.e., it should not override a user-supplied value)

## Why This Matters

This change allows observability platforms and tracing tools that receive LangGraph execution events to automatically detect and categorize them without any additional user configuration. It's a small metadata addition that enables better tooling integration and makes the overall LangGraph ecosystem more interoperable with standard tracing and monitoring infrastructure.
