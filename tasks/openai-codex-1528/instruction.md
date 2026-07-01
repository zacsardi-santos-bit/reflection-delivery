Implement a new library crate to apply code changes from a remote coding agent's task response to a local git repository. Ensure the changes are applied using a 3-way merge strategy, handling conflicts appropriately, and support deserialization of task responses from JSON.

*   Implement the `apply_diff_from_task` function:
    *   Accept a `GetTaskResponse` parameter.
    *   Locate the first 'pr' type item in `current_diff_task_turn.output_items`.
    *   Extract the `diff` string from `output_diff` and apply it to the local git repository using a 3-way merge strategy.
    *   Return `Ok(())` if the diff applies cleanly with no conflicts.
    *   Return `Err` if there are conflicts, leaving standard merge conflict markers in affected files.
    *   Return `Err` if `current_diff_task_turn` is `None` or contains no 'pr' type item.

*   Ensure `GetTaskResponse` is deserializable from JSON:
    *   Include an optional `current_diff_task_turn` field with an `output_items` array.
    *   Deserialize 'pr' type items to a `PrOutputItem` struct with an `output_diff` containing a `diff` string.
    *   Ignore items with other type values without error.

*   Create the `codex-chatgpt` crate at `codex-rs/chatgpt/`:
    *   Add the crate to the Cargo workspace.
    *   Publicly export the `apply_command` module containing `apply_diff_from_task`.
    *   Publicly export the `get_task` module containing `GetTaskResponse` and related types.

*   Define necessary structs and enums:
    *   `GetTaskResponse` struct in `codex-rs/chatgpt/src/get_task.rs`.
    *   `AssistantTurn` struct with an `output_items` field of type `Vec<OutputItem>`.
    *   `OutputItem` enum with a "pr" variant wrapping `PrOutputItem`.
    *   `PrOutputItem` struct with an `output_diff` field of type `OutputDiff`.
    *   `OutputDiff` struct with a `diff` field of type `String`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.