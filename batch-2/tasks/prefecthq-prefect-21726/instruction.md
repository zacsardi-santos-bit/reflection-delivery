I'm running into a problem with the bulk task run event recorder in our workflow orchestration system.

*   When two sequential calls to record_bulk_task_run_events have events with different task_run_ids but the same natural key (flow_run_id, task_key, dynamic_key), the function must NOT raise an IntegrityError. It must update the existing task run's state to the latest value. The first (earlier) task_run_id is the canonical record; the second task_run_id must not exist in the database afterward.

*   When a single batch passed to record_bulk_task_run_events contains two events with different task_run_ids but the same natural key (flow_run_id, task_key, dynamic_key), the LATER event's task_run_id becomes the canonical record. The earlier task_run_id must not exist in the database afterward.

*   When two events share the same task_run_id but differ in task_key or dynamic_key (ID conflict across sequential batches), record_bulk_task_run_events must update the task run's task_key, dynamic_key, and state_type to the values from the later event.

*   When a single batch contains multiple events for the same task_run_id (ID conflict within batch), record_bulk_task_run_events must coalesce them into the latest event's state and metadata, updating task_key and dynamic_key to the later event's values.

*   When a batch contains events that create a chain of conflicts — for example, one event shares an ID with an existing task run, and a second event shares the updated natural key with that first event — the system must coalesce all such conflicts onto the original canonical task run. The duplicate task_run_id must not be stored, and all states from all conflicting events must be attached to the canonical record.

*   For all conflict scenarios (natural key or ID, same batch or sequential batches), all task run states from ALL conflicting events must be persisted with their task_run_id field set to the canonical task run's ID. Additionally, the state_details.task_run_id on each state must also reference the canonical task run ID.

*   The state history on the canonical task run must include all states from all conflicting events in chronological order. For example, if events produced states PENDING and then RUNNING across two conflicting task run entries, the canonical record must show both states in order.

*   When performing bulk upserts, the VALUES rows passed to the database insert statement must be sorted by the conflict key tuple (flow_run_id, task_key, dynamic_key) to ensure deterministic row-level lock acquisition and prevent deadlocks under concurrent recording.


*   Interface details: Type: Function
Name: record_bulk_task_run_events
Location: src/prefect/server/services/task_run_recorder.py
Signature: record_bulk_task_run_events(events: list[ReceivedEvent]) -> None
Description: Records multiple task run events in bulk. Must handle both ID-based and natural-key-based conflicts (where natural key is the tuple of flow_run_id, task_key, dynamic_key) by updating existing records and consolidating all state history onto the canonical task run ID. Must sort inserted rows by conflict key (flow_run_id, task_key, dynamic_key) for deterministic lock ordering.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.