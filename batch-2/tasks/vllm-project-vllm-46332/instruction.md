Implement a system to manage KV cache transfers between prefill and decode workers with heterogeneous tensor parallelism, ensuring correct acknowledgment handling and memory management. Update the system to correctly track and process completion signals from decode workers, preventing premature release of resources.

*   Define `MoRIIOTransferAck` as a NamedTuple in `vllm/distributed/kv_transfer/kv_connector/v1/moriio/moriio_common.py` with:
    *   Fields: `transfer_id` (str), `consumer_tp_size` (int, default 1).
    *   Ensure equality comparison works as expected for NamedTuples.

*   Implement `get_moriio_remote_tp_rank` in `vllm/distributed/kv_transfer/kv_connector/v1/moriio/moriio_connector.py`:
    *   Map local tensor-parallel rank to remote rank based on TP sizes.
    *   Raise `ValueError` with "multiple" if sizes are not integer multiples.

*   Implement `validate_moriio_heterogeneous_tp_kv_heads` in the same file:
    *   Validate KV head configuration for heterogeneous TP.
    *   Raise `NotImplementedError` with "replicated KV heads" for unsupported configurations.

*   Implement `get_moriio_expected_ack_count`:
    *   Return expected acknowledgment count based on producer and consumer TP sizes.
    *   Raise `ValueError` with "multiple" for invalid size relationships.

*   Implement `resolve_moriio_transfer_ack`:
    *   Handle acknowledgment resolution, updating state and returning transfer IDs as needed.
    *   Manage `notification_counts` and `completed_transfer_ids` for tracking.

*   Update `MoRIIOConnectorWorker` in `vllm/distributed/kv_transfer/kv_connector/v1/moriio/moriio_connector.py`:
    *   Add `_consumer_notification_counts` and `_completed_consumer_notifications` attributes.
    *   Implement `get_finished` to process ACKs and manage transfer completion.
    *   Implement `_pop_done_transfers` to send release notifications with `consumer_tp_size`.

*   Update `MoRIIOWrapper.send_notify` in `vllm/distributed/kv_transfer/kv_connector/v1/moriio/moriio_engine.py`:
    *   Accept `message_fields` parameter and merge into msgpack payload when applicable.

*   Ensure `pop_finished_req_ids()` returns a list of `MoRIIOTransferAck` instances, preserving duplicates for acknowledgment counting.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.