Implement support for child workflows (sub-orchestrations) in the Dapr workflow engine to ensure that a parent workflow can invoke a child workflow, pass input to it, and await its completion successfully. Ensure that the parent workflow receives the child's output and uses it as its own return value.

*   Enable the parent workflow to call a child workflow by name, passing input data to it.
    *   Ensure the child workflow receives the input, executes its logic, and returns a result.
*   Ensure the parent workflow successfully receives the child's output and uses it as its own return value.
*   Implement end-to-end sub-orchestration completion, where the root workflow delegates to a child workflow and marks itself as complete.
    *   Ensure `IsComplete()` returns true upon successful completion.
*   Ensure the serialized output of the root orchestration correctly reflects the child workflow's output.
    *   Support Unicode characters in input and output data.
*   Ensure sub-orchestration functions correctly with default engine options and when internal caching is disabled.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.