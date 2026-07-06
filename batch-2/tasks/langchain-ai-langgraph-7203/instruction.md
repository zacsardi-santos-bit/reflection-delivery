I'm working on improving how LangGraph integrates with external observability and tracing tools.

*   When a LangGraph graph executes (both synchronously and asynchronously), all execution event metadata must include the key 'ls_integration' with the value 'langgraph'.

*   The 'ls_integration': 'langgraph' field must appear at every level of the execution hierarchy: the top-level/root graph metadata (e.g., alongside 'thread_id') and all node-level metadata (e.g., alongside 'langgraph_step', 'langgraph_node', 'langgraph_triggers').

*   The 'ls_integration' field must not override an existing value if one is already set in the metadata — it should only be added when not already present.

*   The 'ls_integration' metadata must propagate through all graph execution event types including stream mode message events, run callbacks, subgraph executions, and exception/error event paths.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.