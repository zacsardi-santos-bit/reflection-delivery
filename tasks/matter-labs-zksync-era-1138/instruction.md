Implement a function to convert raw VM events into a sequence of log query records in the zkSync commitment generator. Reorganize the event module into a directory structure and create JSON test vector files to validate the conversion process.

*   Implement the function `convert_vm_events_to_log_queries` in `core/lib/types/src/event/mod.rs`:
    *   Accept a slice of `VmEvent` and return `Vec<LogQuery>`.
    *   Generate a header `LogQuery` for each `VmEvent` with:
        *   `address` set to `EVENT_WRITER_ADDRESS` (0x000000000000000000000000000000000000800d).
        *   `key` encoding `(event.indexed_topics.len() as u64 + 1) + ((event.value.len() as u64) << 32)`.
        *   `written_value` as the event's contract address (20 bytes right-aligned in 32 bytes).
        *   `is_service` set to true.
        *   `tx_number_in_block` as `event.location.1 as u16`.
    *   Follow the header with data `LogQuery` records:
        *   Collect indexed topics and event value bytes split into 32-byte chunks.
        *   Pair these U256 values: first as `key`, second (or U256::zero()) as `written_value`.
    *   Ensure all `LogQuery` records have:
        *   `timestamp` = 0, `aux_byte` = 0, `shard_id` = 0, `read_value` = U256::zero(), `rw_flag` = false, `rollback` = false.
        *   Data records have `is_service` = false.

*   Restructure the event module:
    *   Move contents of `core/lib/types/src/event.rs` to `core/lib/types/src/event/mod.rs`.
    *   Add `mod tests;` to `mod.rs` for compiling `core/lib/types/src/event/tests.rs`.

*   Create JSON test vector files in `core/lib/types/src/event/test_vectors/`:
    *   `event_with_1_topic_and_long_value.json`
    *   `event_with_2_topics.json`
    *   `event_with_3_topics.json`
    *   `event_with_4_topics.json`
    *   `event_with_value_len_1.json`
    *   Each file must contain:
        *   `event`: a serialized `VmEvent`.
        *   `list`: the serialized `Vec<LogQuery>` output of `convert_vm_events_to_log_queries`.

*   Add the `itertools` crate to `core/lib/types/Cargo.toml` for chunk-based pairing of U256 words.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.