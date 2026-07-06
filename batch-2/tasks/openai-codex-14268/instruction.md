I'm working on the memory system in the Codex Rust codebase.

*   The `serialize_filtered_rollout_response_items` function in the `job` submodule of `codex-rs/core/src/memories/phase1.rs` must be declared with `pub(super)` visibility so it is accessible from the parent module's test submodule via `super::job::serialize_filtered_rollout_response_items`.

*   When called with an empty slice of `RolloutItem` values, `serialize_filtered_rollout_response_items` must return `Ok(String)` (no error), where the string is valid JSON that deserializes to an empty `Vec<codex_protocol::models::ResponseItem>`.


*   Interface details: Type: Function
Name: serialize_filtered_rollout_response_items
Location: codex-rs/core/src/memories/phase1.rs (inside the `job` submodule)
Signature: serialize_filtered_rollout_response_items(items: &[RolloutItem]) -> crate::error::Result<String>
Description: Filters and serializes rollout response items for memory stage-1 prompt inclusion. Must have `pub(super)` visibility so that the parent module's test submodule (`phase1_tests.rs`) can access it via `super::job::serialize_filtered_rollout_response_items`. When given an empty slice, must return `Ok(...)` containing a JSON string that deserializes to an empty `Vec<codex_protocol::models::ResponseItem>`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.