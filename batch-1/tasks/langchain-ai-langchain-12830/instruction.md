Implement support for streaming and async execution in the AgentExecutor class. Enable real-time iteration over agent steps both synchronously and asynchronously, and provide a utility to aggregate streamed data. Simplify the iterator API by removing unnecessary parameters and adding run-tracking metadata.

*   Implement synchronous streaming in AgentExecutor:
    *   Add a `stream` method that yields dictionaries during agent execution.
    *   Each dictionary should represent an action event, step/observation event, or final output event.
    *   Ensure the structure of each dictionary includes keys like 'actions', 'steps', 'messages', and 'output'.

*   Implement asynchronous streaming in AgentExecutor:
    *   Add an `astream` method that yields the same dictionary structure as `stream`, but asynchronously.
    *   Use 'async for' iteration to consume the output of `astream`.

*   Support async invocation in AgentExecutor:
    *   Implement the `arun` method to return the final output string asynchronously.
    *   Implement the `acall` method to return a dictionary with 'output' and optionally 'intermediate_steps' when `return_intermediate_steps=True`.

*   Provide a utility function for aggregation:
    *   Update the `add` function to accept an iterable of dictionaries and return a single merged dictionary.
    *   Concatenate list-valued keys and retain the last non-None value for scalar-valued keys.

*   Simplify the iterator API in AgentExecutor:
    *   Modify the `iter` method to accept an `include_run_info` boolean parameter (default False).
    *   When `include_run_info=True`, include the `RUN_KEY` constant in the final output dictionary with a `run_id` attribute.
    *   Remove the requirement for an `async_` parameter, handling async iteration automatically.

*   Implement the AgentExecutorIterator class:
    *   Ensure it implements the standard Python iterator protocol.
    *   Support both synchronous and asynchronous iteration.
    *   Ensure the final dictionary in the iteration includes run metadata when `include_run_info=True`.

*   Handle invalid tool names:
    *   Ensure that when an invalid tool name is used, the observation in `intermediate_steps` contains an error message listing valid tool names.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.