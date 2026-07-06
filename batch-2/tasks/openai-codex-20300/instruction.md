I'm working on the analytics layer for a multi-agent system.

*   When a subagent thread is started via a thread spawn operation that references a parent thread, the analytics reducer must look up the parent thread's connection and assign it to the new subagent thread's internal state so that the subagent inherits the parent's client connection metadata.

*   If the explicit parent thread ID is absent on the subagent thread started input, the parent thread ID must be derived from the subagent source field (specifically from the thread spawn variant that carries a parent thread ID).

*   When a compaction event is ingested for a subagent thread that has no direct connection of its own, the resulting analytics event must include the parent connection's client identifier in the event_params.app_server_client.product_client_id field.

*   The compaction analytics event for a subagent thread must include event_params.parent_thread_id set to the parent thread's UUID string.


*   Interface details: Type: Struct
Name: AnalyticsReducer
Location: codex-rs/analytics/src/reducer.rs
Description: The analytics reducer that processes analytics facts and emits track event requests. Must be constructible via Default. When processing a SubAgentThreadStarted fact where the subagent source is a thread spawn with a parent thread ID, must inherit the parent thread's connection and store it on the subagent thread's state. When a compaction event is subsequently ingested for the subagent thread, must resolve the inherited connection and include the parent connection's client info and parent thread ID in the emitted analytics event payload under event_params.app_server_client.product_client_id and event_params.parent_thread_id respectively.
Signature: ingest(fact: AnalyticsFact, out: &mut Vec<TrackEventRequest>) -> (async method)

Type: Field
Name: parent_thread_id
Location: SubAgentThreadStartedInput struct (codex-rs/analytics/src/ or associated types file)
Description: An optional field on SubAgentThreadStartedInput. When set to None, the parent thread ID is derived from the subagent_source field. The analytics reducer uses this field (falling back to subagent_source) to determine which parent thread's connection to inherit for the new subagent thread.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.