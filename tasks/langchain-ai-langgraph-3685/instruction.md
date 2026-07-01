Implement a state graph using Pydantic BaseModel for structured validation of the internal state, input, and output models. Ensure that the graph operates correctly when these models are used, including compiling, running, streaming, and handling checkpointers.

*   Ensure the StateGraph compiles successfully when constructed with:
    *   A Pydantic BaseModel for the internal state schema.
    *   Separate Pydantic BaseModel classes for `input` and `output` parameters.
*   Implement `invoke()` and `stream()` methods to:
    *   Accept instances of the input model directly.
    *   Initialize the internal state correctly, including fields not present in the input model.
    *   Return results matching the output model's fields.
*   Ensure the graph's input JSON schema:
    *   Is derived from the Pydantic input model, not the full state model.
    *   Includes `$defs` for nested models.
    *   Lists `query` and `inner` fields under `properties` and declares them as `required`.
    *   Uses the input model's class name as the schema `title`.
*   Ensure the graph's output JSON schema:
    *   Is derived from the Pydantic output model, not the full state model.
    *   Lists `answer` and `docs` fields under `properties` and declares them as `required`.
    *   Uses the output model's class name as the schema `title`.
*   When compiled with a checkpointer and `interrupt_after` nodes:
    *   Stream the graph with a Pydantic input model instance and a config dict containing `thread_id`.
    *   End the stream with an `__interrupt__` event.
*   After a checkpointed interrupt:
    *   Resume execution by passing `None` as input to `stream()` with the same thread config.
    *   Correctly return the remaining node outputs.
*   Implement the `update_state()` method to:
    *   Work with Pydantic models for state, input, and output.
    *   Accept a state update dict and an `as_node` argument.
    *   Return a config dict with `configurable` containing `thread_id`, `checkpoint_id`, and `checkpoint_ns` keys.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.